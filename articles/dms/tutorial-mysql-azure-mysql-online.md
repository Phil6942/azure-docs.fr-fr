---
title: 'Tutoriel : Utiliser Azure Database Migration Service pour effectuer une migration en ligne de MySQL vers Azure Database pour MySQL | Microsoft Docs'
description: Découvrez comment effectuer une migration en ligne de MySQL en local vers Azure Database pour MySQL à l’aide d’Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: b39ce138677fc8933b62bd999f20abc21c0ae690
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617952"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutoriel : Migration de MySQL vers Azure Database pour MySQL en ligne à l’aide de DMS
Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance MySQL locale vers [Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/) avec un temps d’arrêt minimal. En d’autres termes, la migration peut être effectuée avec un temps d’arrêt minimal de l’application. Dans ce tutoriel, vous allez migrer l’exemple de base de données **Employés** à partir d’une instance MySQL 5.7 locale vers Azure Database pour MySQL à l’aide d’une activité de migration en ligne dans Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Migrez l’exemple de schéma à l’aide de l’utilitaire mysqldump.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

> [!NOTE]
> Effectuer une migration en ligne à l’aide d’Azure Database Migration Service nécessite la création d’une instance basée sur le niveau de tarification Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft vous recommande de créer une instance Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration et introduire des erreurs.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. La version de MySQL locale doit correspondre à la version d’Azure Database pour MySQL. Par exemple, MySQL 5.6 ne peut migrer que vers Azure Database pour MySQL 5.6, et ne peut pas être migré vers 5.7.
- [Créez une instance dans Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Consultez l’article [Utilisation de MySQL Workbench pour vous connecter et interroger des données](https://docs.microsoft.com/azure/mysql/connect-workbench) pour plus d’informations sur la connexion et la création d’une base de données à l’aide du portail Azure.  
- Créez un réseau virtuel Azure (VNET) pour le service Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou une passerelle [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) au sous-réseau où doit être provisionné le service :
    > - Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > - Point de terminaison de stockage
    > - Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire, car Azure Database Migration Service ne dispose pas d’une connectivité Internet.
 
- Vérifiez que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas les ports de communication entrants suivants à Azure Database Migration Service : 443, 53, 9354, 445, 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur MySQL source, par défaut le port TCP 3306.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Créez une [règle de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de niveau serveur pour Azure Database pour MySQL afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Le serveur MySQL source doit être sur une édition MySQL Community prise en charge. Pour déterminer la version de l’instance MySQL, exécutez la commande suivante dans l’utilitaire MySQL ou MySQL Workbench :
    ```
    SELECT @@version;
    ```
- Azure Database pour MySQL ne prend en charge que les tables InnoDB. Pour convertir les tables MyISAM en InnoDB, consultez l’article [Convertir des tables MyISAM en InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 

- Activez la journalisation binaire dans le fichier my.ini (Windows) ou my.cnf (Unix) dans la base de données source à l’aide de la configuration suivante :

    - **server_id** = 1 ou supérieur (uniquement pour MySQL 5.6)
    - **log-bin** =<path> (uniquement pour MySQL 5.6)

        Par exemple : log-bin = E:\MySQL_logs\BinLog
    - **binlog_format** = row
    - **Expire_logs_days** = 5 (il est recommandé de ne pas utiliser la valeur zéro ; uniquement pour MySQL 5.6)
    - **binlog_row_image** = full (uniquement pour MySQL 5.6)
    - **log_slave_updates** = 1
 
- L'utilisateur doit avoir le rôle ReplicationAdmin avec les privilèges suivants :
    - **CLIENT DE RÉPLICATION** : obligatoire pour les tâches Traitement des changements uniquement. En d’autres termes, les tâches Chargement complet uniquement n’ont pas besoin de ce privilège.
    - **RÉPLICA DE RÉPLICATION** : obligatoire pour les tâches Traitement des changements uniquement. En d’autres termes, les tâches Chargement complet uniquement n’ont pas besoin de ce privilège.
    - **SUPER** : obligatoire uniquement dans les versions antérieures à MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma
Pour compléter tous les objets de base de données tels que les schémas de table, les index et les procédures stockées, nous devons extraire le schéma à partir de la base de données source et l’appliquer à la base de données. Pour extraire le schéma, vous pouvez utiliser mysqldump avec le paramètre `--no-data`.
 
En supposant que vous avez l’exemple de base de données des employés MySQL dans le système local, la commande pour effectuer la migration du schéma en utilisant mysqldump est :
```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```
Par exemple : 
```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Pour importer le schéma vers la cible Azure Database pour MySQL, exécutez la commande suivante :

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Par exemple : 
```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Si vous avez des clés étrangères dans votre schéma, la charge initiale et la synchronisation continue de la migration échouent.  Exécutez le script suivant dans MySQL Workbench pour extraire le script de clé étrangère Drop et ajouter le script de clé étrangère.
```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,    
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```
        
Exécutez la clé étrangère Drop (qui est la deuxième colonne) dans le résultat de la requête pour supprimer la clé étrangère.

> [!IMPORTANT]
> Veillez également à supprimer toutes les instructions DEFINER du schéma afin d’éviter un échec de la migration.

Si vous avez un déclencheur dans les données (déclencheur d’insertion ou de mise à jour), il appliquera l’intégrité des données dans la cible avant les données répliquées à partir de la source. Nous vous recommandons de désactiver les déclencheurs de toutes les tables au niveau de la cible pendant la migration, puis d’activer les déclencheurs une fois la migration terminée.

Pour désactiver les déclencheurs dans la base de données cible, utilisez la commande suivante :
```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration
1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.
 
   ![Afficher les abonnements au portail](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)
       
2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
 
    ![Afficher les fournisseurs de ressources](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)
    
3.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
 
    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)    

## <a name="create-a-dms-instance"></a>Créer une instance de service DMS
1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2.  Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.
 
    ![Créer une instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3.  Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database Managed Instance cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](https://aka.ms/DMSVnet).

5. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    Si vous avez besoin d’aide pour choisir le niveau Azure Database Migration Service adapté, consultez les recommandations du billet de blog [Choosing an Azure Database Migration Service (Azure DMS) tier](https://go.microsoft.com/fwlink/?linkid=861067). 

     ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

7.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.
 
      ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.
 
     ![Rechercher votre instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)
 
3. Sélectionnez **+ Nouveau projet de migration**.
4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet, dans la zone de texte **Type de serveur source**, sélectionnez **MySQL** et, dans la zone de texte **Type de serveur cible**, sélectionnez **AzureDbForMySQL**.
5. Dans la section **Choisir un type d’activité**, sélectionnez **Migration de données en ligne**

    ![Créer un projet Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Une autre possibilité consiste à choisir **Create project only** (Créer uniquement le projet) pour créer le projet de migration à ce stade et exécuter la migration ultérieurement.

6. Sélectionnez **Enregistrer**, notez les conditions requises pour utiliser correctement DMS pour migrer des données, puis sélectionnez **Créer et exécuter une activité**.

## <a name="specify-source-details"></a>Spécifier les détails de la source
1. Dans l’écran **Ajouter les détails de la source**, spécifiez les détails de connexion de l’instance MySQL source.
 
    ![Écran Ajouter les détails de la source](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)   

## <a name="specify-target-details"></a>Spécifier les détails de la cible
1. Sélectionnez **Enregistrer** puis, dans l’écran **Détails de la cible**, spécifiez les détails de connexion du serveur Azure Database pour MySQL cible, à savoir l’instance Azure Database pour MySQL pré-provisionnée sur laquelle le schéma **Employés** a été déployé à l’aide de mysqldump.

    ![Écran Détails de la cible](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Sélectionnez **Enregistrer**, puis dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    ![Mapper aux bases de données cibles](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3.  Sélectionnez **Enregistrer**, dans l’écran **Récapitulatif de la migration** pour la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration, puis examinez le récapitulatif pour vous assurer que les détails de la source et de la cible correspondent à ceux que vous avez spécifiés précédemment.

    ![Résumé de la migration](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Exécuter la migration
- Sélectionnez **Exécuter la migration**.

    La fenêtre d’activité de migration s’affiche, et le champ **État** de l’activité affiche **Initialisation en cours**.

## <a name="monitor-the-migration"></a>Surveiller la migration
1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Terminé**.

     ![État d’activité : Terminé](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Sous **Nom de la base de données**, sélectionnez une base de données spécifique pour obtenir l’état de migration des opérations **Chargement complet des données en cours** et **Synchronisation des données incrémentielles**.

    L’opération Charge complète des données affichera l’état de migration de la charge initiale, tandis que l’opération Synchronisation des données incrémentielles indiquera l’état de capture des changements de données (CDC).
   
     ![État de l’activité : Charge complète terminée](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![État de l’activité : Synchronisation des données incrémentielles](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Effectuer le basculement de la migration
Une fois le chargement complet initial effectué, les bases de données identifiées par le libellé **Prêt pour le basculement**.

1. Lorsque vous êtes prêt à effectuer la migration de base de données, sélectionnez **Démarrer le basculement**.

    ![Démarrer le basculement](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)
 
2.  Veillez à arrêter toutes les transactions entrantes pour la base de données source ; attendez que le compteur **Changements en attente** indique la valeur **0**.
3.  Sélectionnez **Confirmer**, puis **Appliquer**.
4. Lorsque la migration de base de données présente l’état **Terminé**, connectez vos applications à la nouvelle instance Azure SQL Database cible.
 
## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les limitations et problèmes connus lors de l’exécution de migrations en ligne vers Azure Database pour MySQL, consultez l’article [Known issues and workarounds with Azure Database for MySQL online migrations](known-issues-azure-mysql-online.md) (Problèmes connus et solutions de contournement pour les migrations Azure Database pour MySQL en ligne).
- Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](https://docs.microsoft.com/azure/dms/dms-overview).
- Pour plus d’informations sur Azure Database pour MySQL, consultez l’article [Qu’est-ce qu’Azure Database pour MySQL ?](https://docs.microsoft.com/azure/mysql/overview).

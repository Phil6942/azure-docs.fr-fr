---
title: Gestion des bases de données cloud avec montée en charge | Microsoft Docs
description: Utilisez le service des tâches de base de données élastique pour exécuter un script dans un groupe de bases de données.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8abb2e3ac4f62a3ea51cc686bbf23260fccc4077
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441370"
---
# <a name="managing-scaled-out-cloud-databases"></a>Gestion des bases de données cloud avec montée en charge

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Bases de données élastiques** est un Service hébergé par le client de Cloud Azure qui permet l’exécution de tâches d’administration ad hoc et planifiées, appelées **travaux**. Grâce aux travaux, vous pouvez facilement gérer, de façon fiable, de grands groupes de bases de données SQL Azure en exécutant les scripts Transact-SQL pour effectuer des opérations administratives.

Pour gérer les bases de données partitionnées avec montée en charge, la fonction **Tâches de base de données élastique** (version préliminaire) vous permet d’exécuter un script Transact-SQL (T-SQL) fiable dans un groupe de bases de données, notamment :

- une collection personnalisée des bases de données (voir ci-après)
- toutes les bases de données dans un [pool élastique](sql-database-elastic-pool.md)
- un ensemble de partitions (créé à l’aide d’une [bibliothèque client de base de données élastique](sql-database-elastic-database-client-library.md)).

Pour plus d’informations sur le concept d’une base de données partitionnée, consultez [Sharding A SQL Server Database](https://blog.pythian.com/sharding-sql-server-database/) (Partitionnement d’une base de données SQL Server).

## <a name="documentation"></a>Documentation

- [Installer les composants de tâches de base de données élastique](sql-database-elastic-jobs-service-installation.md).
- [Prise en main des tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md).
- [Créer et gérer des tâches avec PowerShell](sql-database-elastic-jobs-powershell.md).
- [Créer et gérer des bases de données SQL Azure avec montée en charge](sql-database-elastic-jobs-getting-started.md)

![Service de tâche de base de données élastique][1]

## <a name="why-use-jobs"></a>Pourquoi utiliser les travaux ?

### <a name="manage"></a>gérer

Effectuer facilement des modifications de schéma, la gestion des informations d’identification, les mises à jour de données de référence, la collecte des données de performances ou la collecte télémétrique du locataire (client).

### <a name="reports"></a>Rapports

Agréger des données provenant d’une collection de bases de données SQL Azure dans un tableau de destination unique.

### <a name="reduce-overhead"></a>Réduire la surcharge

Normalement, vous devez vous connecter indépendamment à chaque base de données pour exécuter les instructions Transact-SQL ou effectuer d'autres tâches administratives. Un travail gère la tâche de connexion à chaque base de données dans le groupe cible. Vous pouvez également définir, gérer et conserver des scripts Transact-SQL à exécuter dans un groupe de bases de données SQL Azure.

### <a name="accounting"></a>Comptabilité

Les tâches exécutent le script et enregistrent l’état d’exécution pour chaque base de données. De plus, une nouvelle tentative est lancée automatiquement en cas d’échec.

### <a name="flexibility"></a>Flexibilité

Définir des groupes personnalisés de bases de données SQL Azure, ainsi que des planifications pour l’exécution d’un travail.

> [!NOTE]
> Dans le portail Azure, seul un ensemble réduit de fonctions limitées aux pools élastiques SQL Azure est disponible. Utiliser les APIs PowerShell pour accéder à l'ensemble des fonctionnalités actuelles.

## <a name="applications"></a>APPLICATIONS

- Réaliser des tâches administratives, par exemple le déploiement d'un nouveau schéma.
- Mettre à jour les informations données-produits de référence sur toutes les bases de données. Ou planifier des mises à jour automatiques tous les jours ouvrables, après les heures de travail.
- Recréer des indexes pour améliorer les performances d’une requête. La reconstruction peut être configurée pour s'exécuter dans une collection de bases de données de façon récurrente, par exemple pendant les heures creuses.
- Collecter les résultats de la requête à partir d'un ensemble de bases de données dans une table centrale sur une base continue. Les requêtes de performances peuvent être exécutées en permanence et configurées pour déclencher des tâches supplémentaires à exécuter.
- Exécuter des requêtes de traitement de données avec un temps d’exécution plus long sur un grand ensemble de bases de données, par exemple, la collection de télémétrie de client. Les résultats sont rassemblés dans une table de destination unique pour une analyse ultérieure.

## <a name="elastic-database-jobs-end-to-end"></a>Travaux de base de données élastique : de bout en bout

1. Installez les composants de **travaux de bases de données élastiques** . Pour en savoir plus, consultez [Installation des tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md). Si l'installation échoue, voir [comment désinstaller](sql-database-elastic-jobs-uninstall.md).
2. Utilisez les APIs PowerShell pour accéder à davantage de fonctionnalités, par exemple la création de collections de bases de données personnalisées, l’ajout de planifications et/ou la collecte des ensembles de résultats. Utilisez le portail pour une installation simple et la création/la surveillance de travaux limités à l’exécution par rapport à un **pool élastique**.
3. Créez des informations d’identification chiffrées pour l’exécution d’une tâche et [ajoutez l’utilisateur (ou le rôle) à chaque base de données du groupe](sql-database-security-overview.md).
4. Créez un script T-SQL idempotent qui peut être exécuté sur chacune des bases de données du pool.
5. Suivez ces étapes pour créer des travaux à partir du portail Azure : [Création et gestion de tâches de base de données élastique](sql-database-elastic-jobs-create-and-manage.md).
6. Ou utilisez des scripts PowerShell : [Créer et gérer des tâches de base de données élastique SQL Database en utilisant PowerShell (préversion)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Scripts idempotents

Les scripts doivent être [idempotent](https://en.wikipedia.org/wiki/Idempotence). En termes simples, « idempotent » signifie que si le script réussit, et est réexécuté, le résultat est le même. Un script peut échouer en raison de problèmes réseau provisoires. Dans ce cas, le travail va automatiquement refaire des tentatives d’exécution du script un nombre prédéfini de fois. Un script idempotent a le même résultat, même s’il a été correctement exécuté deux fois.

Une tactique simple consiste à tester l’existence d’un objet avant de créer ce dernier.  

```sql
    IF NOT EXIST (some_object)
    -- Create the object
```

De même, un script doit être capable d’exécuter avec succès par une vérification logique et s’adapter aux conditions qu’il rencontre.

## <a name="failures-and-logs"></a>Échecs et journaux d’activité

Si un script échoue après plusieurs tentatives, le travail consigne l’erreur et continue. Après la fin d’un travail (en d’autres termes, son exécution sur toutes les bases de données dans le groupe), vous pouvez vérifier la liste de tentatives ayant échoué. Les journaux d’activité fournissent des détails pour déboguer des scripts défectueux.

## <a name="group-types-and-creation"></a>Types de groupe et création

Il existe deux types de groupes :

1. Ensemble de partitions
2. Groupes personnalisés

Les groupes d’ensembles de partitions sont créés à l’aide des [outils de base de données élastique](sql-database-elastic-scale-introduction.md). Lorsque vous créez un groupe de jeu de partitions, des bases de données sont ajoutées au groupe ou supprimées de ce dernier automatiquement. Par exemple, une nouvelle partition est automatiquement créée dans le groupe quand vous l’ajoutez à la carte de partitions. Une tâche peut ensuite être exécutée sur le groupe.

Les groupes personnalisés, quant à eux, sont définis de façon rigide. Vous devez explicitement ajouter ou supprimer des bases de données à partir de groupes personnalisés. Si une base de données du groupe est supprimée, le travail va tenter d’exécuter le script sur la base de données, ce qui entraînera une panne éventuelle. Les groupes créés actuellement à l’aide du portail Azure sont des groupes personnalisés.

## <a name="components-and-pricing"></a>Composants et tarification

Les composants suivants fonctionnent ensemble pour créer un service Cloud Azure qui permet une exécution ad hoc de tâches administratives. Les composants sont installés et configurés automatiquement pendant l'installation, dans le cadre de votre abonnement. Vous pouvez identifier les services car ils portent tous le même nom généré automatiquement. Le nom est unique et se compose d'un préfixe « edj » suivi de 21 caractères générés de façon aléatoire.

- Service cloud Azure

  Les travaux de base de données élastiques (préversion) sont fournis sous la forme d’un service Cloud Azure hébergé par le client pour exécuter les tâches demandées. À partir du portail, le service est déployé et hébergé dans votre abonnement Microsoft Azure. Par défaut, le service déployé s'exécute avec un minimum de deux rôles de travail pour garantir une haute disponibilité. La taille par défaut de chaque rôle de travail (ElasticDatabaseJobWorker) s'exécute sur une instance A0. Pour la tarification, voir [Tarification des services cloud](https://azure.microsoft.com/pricing/details/cloud-services/).

- Azure SQL Database

  Le service utilise une base de données SQL Azure appelée **base de données de contrôle** pour stocker toutes les métadonnées du travail. Le niveau de service par défaut est S0. Pour en savoir plus, voir [Tarification - SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

- Azure Service Bus

  Azure Service Bus sert à coordonner le travail au sein du service Cloud Azure. Voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

- Stockage Azure

  Un compte Stockage Azure sert à stocker la journalisation des résultats de diagnostic au cas où un problème nécessite un débogage supplémentaire (consultez [Activation des diagnostics Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)). Pour la tarification, voir [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Fonctionnement des tâches de bases de données élastiques

1. Une base de données SQL Azure est conçue comme une **base de données de contrôle** qui stocke toutes les métadonnées et les données d’état.
2. Pour accéder à la base de données de contrôle, accédez au **service des tâches** pour lancer les tâches à exécuter et en assurer le suivi.
3. Deux rôles communiquent avec la base de données de contrôle :
   - Contrôleur : détermine les travaux qui nécessitent des tâches pour être menées à bien et tente à nouveau les tâches ayant échoué en créant de nouvelles tâches de travail.
   - Exécution de tâches de travail : mène à bien les tâches de travail.

### <a name="job-task-types"></a>Types de tâches de travail

Il existe plusieurs types de tâches qui effectuent l'exécution des tâches :

- ShardMapRefresh

  Interroge la carte de partitions pour déterminer toutes les bases de données utilisées en tant que partitions
- ScriptSplit

  Répartit le script sur les instructions « GO » dans des lots
- ExpandJob

  Crée des travaux enfants pour chaque base de données à partir d’un travail qui cible un groupe de bases de données
- ScriptExecution

  Exécute un script sur une base de données spécifique à l’aide d’informations d’identification définies
- Dacpac

  Applique un DACPAC à une base de données spécifique à l’aide d’informations d’identification spécifiques

## <a name="end-to-end-job-execution-work-flow"></a>Flux de travail de l'exécution de tâche de bout en bout

1. Une tâche est insérée dans la **base de données de contrôle** à l’aide du portail ou de l’API PowerShell. La tâche demande l'exécution d'un script Transact-SQL sur un groupe de bases de données à l'aide des informations d'identification spécifiques.
2. Le contrôleur identifie la nouvelle tâche. Les tâches sont créées et exécutées pour fractionner le script et pour actualiser les bases de données du groupe. Enfin, une nouvelle tâche est créée et exécutée pour développer la tâche et créer des tâches enfants où chacune de celles-ci est configurée pour exécuter le script Transact-SQL sur une base de données individuelle du groupe.
3. Le contrôleur identifie la nouvelle tâche enfant. Pour chaque tâche, le contrôleur crée et déclenche une tâche pour exécuter le script sur une base de données.
4. Une fois toutes les tâches terminées, le contrôleur met à jour et définit les tâches sur l’état terminé.
   À tout moment pendant l'exécution de la tâche, l'API PowerShell peut être utilisée pour afficher l'état actuel de l'exécution de la tâche. Toutes les heures retournées par les APIs PowerShell sont représentées au format UTC. Si vous le souhaitez, une demande d'annulation peut être lancée pour arrêter une tâche.

## <a name="next-steps"></a>Étapes suivantes

[Installez les composants](sql-database-elastic-jobs-service-installation.md), puis [créez et ajoutez un journal dans chaque base de données du groupe](sql-database-manage-logins.md). Pour mieux comprendre la création et la gestion de tâches, consultez la page [Création et gestion de tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md). Consultez également [Prise en main des tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

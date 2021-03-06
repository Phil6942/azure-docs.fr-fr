---
title: Surveillance et dépannage à partir de HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Surveillance et dépannage à partir de Hana sur SAP HANA sur Azure (grandes Instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15b3fb4ae483e5b0e4f930d0dc08de6d198d0e5f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005245"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Surveillance et dépannage à partir de HANA

Pour analyser efficacement les problèmes liés à SAP HANA sur Azure (grandes instances), il est utile d’identifier la cause principale d’un problème. SAP a publié de nombreux documents pour vous aider.

Des FAQ pertinentes liées aux performances de SAP HANA se trouvent dans les Notes SAP suivantes :

- [Note SAP #2222200 – FAQ : Réseau SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [Note SAP #2100040 – FAQ : PROCESSEUR SAP HANA](https://launchpad.support.sap.com/#/notes/0002100040)
- [Note SAP #199997 – FAQ : Mémoire SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [Note SAP #200000 – FAQ : Optimisation des performances de SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Note SAP #199930 – FAQ : Analyse SAP HANA d’e/s](https://launchpad.support.sap.com/#/notes/1999930)
- [Note SAP #2177064 – FAQ : Incidents et redémarrage du Service SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertes SAP HANA

Dans un premier temps, vérifiez les journaux d’activité d’alerte SAP HANA. Dans SAP HANA Studio, accédez à **Console d’Administration : Alertes : Afficher : toutes les alertes**. Cet onglet affiche toutes les alertes SAP HANA ayant des valeurs spécifiques (mémoire physique disponible, utilisation du processeur, etc.) qui se situent en dehors des seuils minimum et maximum définis. Par défaut, les vérifications sont effectuées automatiquement toutes les 15 minutes.

![Dans SAP HANA Studio, accédez à la Console d’Administration : Alertes : Afficher : toutes les alertes](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>UC

Pour résoudre une alerte déclenchée en raison d’un paramètre hors du seuil, vous pouvez rétablir la valeur par défaut ou définir une valeur de seuil plus raisonnable.

![Rétablir la valeur par défaut ou définir une valeur de seuil plus raisonnable](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Les alertes suivantes peuvent également indiquer des problèmes de ressources de processeur :

- Utilisation du processeur hôte (alerte 5)
- Dernière opération de point de sauvegarde (alerte 28)
- Durée du point de sauvegarde (alerte 54)

Les éléments suivants peuvent vous aider à identifier une consommation de processeur élevée sur votre base de données SAP HANA :

- L’alerte 5 (Utilisation du processeur hôte) est déclenchée pour l’utilisation actuelle ou passée du processeur
- L’utilisation du processeur affichée sur l’écran Vue d’ensemble

![Utilisation du processeur affichée sur l’écran Vue d’ensemble](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Le graphique de charge peut indiquer une consommation du processeur élevée, ou une consommation élevée par le passé :

![Le graphique de charge peut indiquer une consommation du processeur élevée, ou une consommation élevée par le passé](./media/troubleshooting-monitoring/image4-load-graph.png)

Une alerte déclenchée en raison d’une utilisation élevée du processeur peut être dû à plusieurs raisons, y compris, mais sans limitation : l’exécution de certaines transactions, le chargement des données, les travaux qui ne répondre pas, longues instructions SQL et les performances de requête incorrectes (par exemple, avec BW sur des cubes HANA).

Reportez-vous à la [SAP HANA résolution des problèmes : Processeur connexes Causes et Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) de site pour les étapes de dépannage détaillées.

## <a name="operating-system"></a>Système d’exploitation

L’une des principales vérifications à effectuer pour SAP HANA sur Linux est de s’assurer que les THP (Transparent Huge Pages) sont désactivées. Consultez la [Note SAP #2131662 – THP (Transparent Huge Pages) sur les serveurs SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Vous pouvez vérifier si les Transparent Huge Pages sont activées via la commande Linux suivante : **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Si _always_ apparaît entre crochets, comme ci-dessous, cela signifie que les THP sont activées : [always] madvise never ; si _never_ est placé entre crochets, comme ci-dessous, cela signifie que les THP sont désactivées : always madvise [never]

La commande Linux suivante ne doit renvoyer aucun résultat : **rpm -qa | grep ulimit.** Si _ulimit_ est installé, désinstallez-le immédiatement.

## <a name="memory"></a>Mémoire

Vous pouvez voir que la quantité de mémoire allouée par la base de données SAP HANA est plus élevée que prévu. Les alertes suivantes indiquent des problèmes liés à une utilisation élevée de la mémoire :

- Utilisation de la mémoire physique de l’hôte (alerte 1)
- Utilisation de la mémoire du serveur de noms (alerte 12)
- Utilisation totale de la mémoire des tables de la banque des colonnes (alerte 40)
- Utilisation de la mémoire des services (alerte 43)
- Utilisation de la mémoire du stockage principal des tables de la banque des colonnes (alerte 45)
- Fichiers de vidage runtime (alerte 46)

Reportez-vous à la [SAP HANA résolution des problèmes : Problèmes de mémoire](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) de site pour les étapes de dépannage détaillées.

## <a name="network"></a>Réseau

Reportez-vous à la [Note SAP #2081065 – Résolution des problèmes de réseau SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) et suivez les étapes de dépannage réseau qui y sont décrites.

1. Analyse des temps d’aller-retour entre le client et le serveur.
  R. Exécutez le script SQL [_HANA\_Réseau\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysez la communication entre les nœuds.
  R. Exécutez le script SQL [_HANA\_Réseau\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Exécutez la commande Linux **ifconfig** (la sortie indique si des pertes de paquets se produisent).
4. Exécutez la commande Linux **tcpdump**.

Utilisez également l’outil open source [IPERF](https://iperf.fr/) (ou tout outil similaire) pour mesurer les véritables performances du réseau d’application.

Reportez-vous à la [SAP HANA résolution des problèmes : Problèmes de connectivité et de performances de mise en réseau](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) de site pour les étapes de dépannage détaillées.

## <a name="storage"></a>Stockage

À partir d’un point de vue de l’utilisateur final, une application (ou le système dans sa globalité) s’exécute lentement, ne répond pas ou peut même sembler cesser de répondre si des problèmes avec des performances d’e/s. L’onglet **Volumes** de SAP HANA Studio répertorie les volumes associés et les volumes utilisés par chaque service.

![L’onglet Volumes de SAP HANA Studio répertorie les volumes associés et les volumes utilisés par chaque service](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Pour les volumes associés figurant dans la partie inférieure de l’écran, vous pouvez voir les détails des volumes, par exemple les fichiers et les statistiques d’E/S.

![Pour les volumes associés figurant dans la partie inférieure de l’écran, vous pouvez voir les détails des volumes, par exemple les fichiers et les statistiques d’E/S](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Reportez-vous à la [SAP HANA résolution des problèmes : E/s liés les Causes et Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) et [SAP HANA résolution des problèmes : Solutions et Causes principales liées au disque](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) de site pour les étapes de dépannage détaillées.

## <a name="diagnostic-tools"></a>Outils de diagnostic

Effectuez une vérification d’intégrité de SAP HANA via HANA\_Configuration\_Minichecks. Cet outil signale les problèmes techniques critiques potentiels qui devraient déjà avoir déclenché une alerte dans SAP HANA Studio.

Reportez-vous à la [Note SAP #1969700 – collection d’instructions SQL pour SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) et téléchargez le fichier SQL Statements.zip associé à cette note. Enregistrez ce fichier .zip sur le disque dur local.

Dans SAP HANA Studio, dans l’onglet **System Information** (Informations système), cliquez avec le bouton droit de la souris dans la colonne **Name** (Nom) et sélectionnez **Import SQL Statements** (Importer des instructions SQL).

![Dans SAP HANA Studio, dans l’onglet System Information (Informations système), cliquez avec le bouton droit de la souris dans la colonne Name (Nom) et sélectionnez Import SQL Statements (Importer des instructions SQL)](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Sélectionnez le fichier SQL Statements.zip enregistré sur votre ordinateur pour qu’un dossier contenant les instructions SQL correspondantes soit importé. À ce stade, plusieurs vérifications de diagnostics peuvent être effectuées avec ces instructions SQL.

Par exemple, pour tester les besoins en bande passante de réplication système SAP HANA, avec le bouton droit le **la bande passante** instruction sous **réplication : La bande passante** et sélectionnez **Open** dans la Console SQL.

L’instruction SQL s’ouvre. Vous pouvez alors modifier puis exécuter les paramètres d’entrée (section Modification).

![L’instruction SQL s’ouvre. Vous pouvez alors modifier puis exécuter les paramètres d’entrée (section Modification)](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Un autre exemple est avec le bouton droit sur les instructions sous **réplication : Vue d’ensemble**. Sélectionnez **Execute** (Exécuter) dans le menu contextuel :

![Un autre exemple est avec le bouton droit sur les instructions sous Replication : Vue d’ensemble. Sélectionnez Execute (Exécuter) dans le menu contextuel](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Vous obtenez ainsi des informations qui vous aident à résoudre le problème :

![Vous obtenez ainsi des informations qui vous aident à résoudre le problème](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faites de même pour HANA\_Configuration\_Minichecks et vérifiez les marques _X_ dans la colonne _C_ (Critique).

Exemples de sortie :

**HANA\_Configuration\_MiniChecks\_Rev102.01+1** pour les vérifications SAP HANA générales.

![HANA\_Configuration\_MiniChecks\_Rev102.01+1 pour les vérifications SAP HANA générales](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_Overview** pour une vue d’ensemble des exécutions actuelles des services SAP HANA.

![HANA\_Services\_Overview pour une vue d’ensemble des exécutions actuelles des services SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_Statistics** pour les informations de service SAP HANA (processeur, mémoire, etc.).

![HANA\_Services\_Statistics pour les informations de service SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_Overview\_Rev110 +** pour des informations générales sur l’instance SAP HANA.

![HANA\_Configuration\_Overview\_Rev110 + pour des informations générales sur l’instance SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_Parameters\_Rev70+** pour vérifier les paramètres de SAP HANA.

![HANA\_Configuration\_Parameters\_Rev70+ pour vérifier les paramètres de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Étapes suivantes**

- Voir [Configuration de la haute disponibilité dans SUSE à l’aide de STONITH](ha-setup-with-stonith.md).
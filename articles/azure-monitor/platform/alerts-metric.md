---
title: Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor
description: Découvrez comment utiliser le portail Azure ou l’interface CLI pour créer, afficher et gérer des règles d’alerte de métrique.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 117b65265c853194e93a97fe5e2b2dcc6e9f5bc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807386"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor

Dans Azure Monitor, les alertes de métrique vous avertissent lorsque l’une de vos métriques dépasse un certain seuil. Les alertes de métrique fonctionnent sur toute une gamme de métriques de plateforme multidimensionnelles, de métriques personnalisées, mais aussi de métriques standard et personnalisées d’Application Insights. Dans cet article, nous allons décrire comment créer, afficher et gérer des règles d’alerte de métrique via le portail Azure et Azure CLI. Vous pouvez également créer des règles d’alerte de métrique à l’aide de modèles Azure Resource Manager. Cette méthode est décrite dans [un autre article](alerts-metric-create-templates.md).

Vous pouvez en savoir plus sur le fonctionnement des alertes de métrique dans la [vue d’ensemble des alertes de métrique](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Créer avec le Portail Azure

La procédure suivante décrit comment créer une règle d’alerte de métrique dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Moniteur**. Le panneau Moniteur consolide tous vos paramètres et données de supervision dans une même vue.

2. Cliquez sur **Alertes**, puis sur **+ Nouvelle règle d’alerte**.

    > [!TIP]
    > La plupart des panneaux de ressources incluent également l’option **Alertes** dans leur menu de ressources, sous **Supervision**. Vous pouvez créer des alertes à partir de là également.

3. Cliquez sur **Sélectionner une cible** dans le volet contextuel qui se charge, puis sélectionnez une ressource cible sur laquelle définir une alerte. Utilisez les listes déroulantes **Abonnement** et **Type de ressource** pour rechercher la ressource à surveiller. Vous pouvez également utiliser la barre de recherche pour rechercher votre ressource.

4. Si la ressource sélectionnée dispose de métriques pour lesquelles vous pouvez créer des alertes, la zone des **signaux disponibles** dans la partie inférieure droite inclura des métriques. Vous pouvez afficher la liste complète des types de ressources pris en charge pour les alertes de métrique dans cet [article](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Une fois que vous avez sélectionné une ressource cible, cliquez sur **Ajouter une condition**.

6. Vous verrez alors une liste des signaux pris en charge pour la ressource. Sélectionnez la métrique sur laquelle vous souhaitez créer une alerte.

7. Si vous le souhaitez, affinez la métrique en ajustant la **Période** et **l’Agrégation**. Si la métrique a des dimensions, une table **Dimensions** s’affiche. Sélectionnez une ou plusieurs valeurs par dimension. L’alerte de métrique évaluera la condition pour toutes les combinaisons de valeurs sélectionnées. [En savoir plus sur le fonctionnement de la génération d’alertes sur les métriques multidimensionnelles](alerts-metric-overview.md). Vous pouvez également **sélectionner \*** pour les dimensions. **Sélectionner \*** mettra à l’échelle la sélection de manière dynamique en fonction de toutes les valeurs actuelles et futures pour une dimension.

8. Vous verrez un graphique de la métrique pendant les 6 dernières heures. Définissez les paramètres de l’alerte : le **type de condition**, la **fréquence**, **l’opérateur** et le **seuil** ou la **sensibilité** afin de déterminer la logique que la règle d’alerte de métrique évaluera. [En savoir plus sur le type de condition Seuils dynamiques et les options de sensibilité](alerts-dynamic-thresholds.md).

9. Si vous utilisez un seuil statique, le graphique de métriques peut vous aider à déterminer ce qui pourrait être un seuil raisonnable. Si vous utilisez des seuils dynamiques, le graphique de métriques affiche les seuils calculés en fonction des données récentes.

10. Cliquez sur **Terminé**.

11. Si vous le souhaitez, ajoutez un autre critère pour surveiller une règle d’alerte complexe. Actuellement, les utilisateurs peuvent avoir des règles d’alerte avec un critère Seuils dynamiques en tant que critère unique.

12. Renseignez les **détails de l’alerte** (**Nom de règle d’alerte**, **Description** et **Gravité** par exemple).

13. Ajoutez un groupe d’actions à l’alerte, soit en sélectionnant un groupe d’actions existant, soit en créant un nouveau groupe d’actions.

14. Cliquez sur **Terminé** pour enregistrer la règle d’alerte de métrique.

> [!NOTE]
> Les règles d’alerte de métrique créées via le portail sont créées dans le même groupe de ressources que la ressource cible.

## <a name="view-and-manage-with-azure-portal"></a>Afficher et gérer avec le Portail Azure

Vous pouvez afficher et gérer les règles d’alerte de métrique à l’aide du panneau Gérer les règles, sous Alertes. La procédure suivante vous explique comment afficher vos règles d’alerte de métrique et comment en modifier une.

1. Dans le portail Azure, accédez à **Moniteur**.

2. Cliquez sur **Alertes** et **Gérer les règles**.

3. Dans le panneau **Gérer les règles**, vous pouvez afficher toutes vos règles d’alerte dans tous les abonnements. Vous pouvez filtrer davantage les règles à l’aide des options **Groupe de ressources**, **Type de ressource** et **Ressource**. Si vous souhaitez afficher uniquement les alertes de métrique, sélectionnez le **type de signal** Métriques.

    > [!TIP]
    > Dans le panneau **Gérer les règles**, vous pouvez sélectionner plusieurs règles d’alerte et les activer/désactiver. Cela peut être utile lorsque certaines ressources cibles doivent être mises en maintenance.

4. Cliquez sur le nom de la règle d’alerte de métrique que vous souhaitez modifier.

5. Dans Modifier la règle, cliquez sur le **critère d’alerte** que vous souhaitez modifier. Vous pouvez modifier la métrique, la condition de seuil et les autres champs en fonction des besoins

    > [!NOTE]
    > Vous ne pouvez pas modifier la **ressource cible** et le **nom de la règle d’alerte** une fois l’alerte de métrique créée.

6. Cliquez sur **Terminé** pour enregistrer vos modifications.

## <a name="with-azure-cli"></a>Avec l’interface de ligne de commande Azure

Les sections précédentes décrivaient comment créer, afficher et gérer des règles d’alerte de métrique à l’aide du portail Azure. Cette section décrit comment faire de même à l’aide de l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) multiplateforme. Le plus rapide pour commencer à utiliser Azure CLI est de s’appuyer sur [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Pour cet article, nous utiliserons Cloud Shell.

1. Accédez au portail Azure, puis cliquez sur **Cloud Shell**.

2. À l’invite, vous pouvez utiliser des commandes avec l’option ``--help`` pour en savoir plus sur la commande et comment l’utiliser. Par exemple, la commande suivante vous montre la liste des commandes disponibles pour la création, l’affichage et la gestion des alertes de métrique.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Vous pouvez créer une seule règle d’alerte de métrique qui surveille si le pourcentage d’UC moyen sur une machine virtuelle est supérieur à 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Vous pouvez afficher toutes les alertes de métrique dans un groupe de ressources à l’aide de la commande suivante.

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Vous pouvez voir les détails d’une règle d’alerte de métrique spécifique en utilisant le nom ou l’ID de ressource de la règle.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Vous pouvez désactiver une règle d’alerte de métrique à l’aide de la commande suivante.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Vous pouvez supprimer une règle d’alerte de métrique à l’aide de la commande suivante.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Créer des alertes de métrique à l’aide de modèles Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md)
- [Comprendre le fonctionnement des alertes de métrique](alerts-metric-overview.md)
- [Comprendre le fonctionnement des alertes de métrique avec la condition Seuils dynamiques](alerts-dynamic-thresholds.md).
- [Comprendre le schéma des webhooks pour les alertes de métrique](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)


---
title: Compteurs de performances dans Application Insights | Microsoft Docs
description: Surveillez les compteurs de performances système et .NET personnalisés dans Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256979"
---
# <a name="system-performance-counters-in-application-insights"></a>Compteurs de performances système dans Application Insights

Windows offre un large éventail de [compteurs de performance](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) tels que le niveau d’occupation du processeur, la mémoire, le disque et l’utilisation du réseau. Vous pouvez également définir vos propres compteurs de performances. Tant que votre application est exécutée sous IIS sur un hôte ou une machine virtuelle local sur lequel vous disposez d’un accès administratif.

## <a name="view-counters"></a>Afficher des compteurs

Le volet Métriques affiche un ensemble de compteurs de performances par défaut.

![Compteurs de performances signalés dans Application Insights](./media/performance-counters/performance-counters.png)

Les compteurs par défaut actuels qui sont collectés pour les applications web .NET sont les suivants :

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Pour afficher tous vos graphiques utiles au même emplacement, créez un [tableau de bord](../../azure-monitor/app/app-insights-dashboards.md) et épinglez-les à celui-ci.

## <a name="add-counters"></a>Ajouter des compteurs

Si le compteur de performances que vous souhaitez n’est pas inclus dans la liste des métriques, vous pouvez l’y ajouter.

1. Découvrez les compteurs disponibles sur votre serveur à l’aide de la commande PowerShell suivante sur le serveur local :
   
    `Get-Counter -ListSet *`
   
    (Voir [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Ouvrez ApplicationInsights.config.
   
   * Si vous avez ajouté Application Insights à votre application pendant le développement, modifiez ApplicationInsights.config dans votre projet, puis redéployez-le sur vos serveurs.
   * Si vous avez utilisé Status Monitor pour instrumenter une application web en cours d’exécution, recherchez ApplicationInsights.config dans le répertoire racine de l’application dans IIS. Mettez-le à jour dans chaque instance de serveur.
3. Modifiez la directive du collecteur de performances :
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Vous pouvez capturer les compteurs standard et ceux que vous avez implémentés vous-même. `\Objects\Processes` est un exemple de compteur standard disponible sur tous les systèmes Windows. `\Sales(photo)\# Items Sold` est un exemple de compteur personnalisé qui peut être implémenté dans un service web. 

Le format est le suivant : `\Category(instance)\Counter"` ou, pour les catégories qui ne présentent aucune instance : `\Category\Counter`, tout simplement.

`ReportAs` est requis pour les noms de compteurs qui ne correspondent pas `[a-zA-Z()/-_ \.]+`, à savoir qu’ils contiennent des caractères ne se trouvant pas dans les ensembles suivants : lettres, crochets, barre oblique, trait d’union, trait de soulignement, espace, point.

Si vous spécifiez une instance, elle est collectée en tant que dimension « CounterInstanceName » de la mesure signalée.

### <a name="collecting-performance-counters-in-code"></a>Collecte des compteurs de performances dans le code
Pour collecter les compteurs de performances système et les envoyer à Application Insights, vous pouvez adapter l’extrait de code ci-dessous :


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Ou vous pouvez effectuer la même opération avec les mesures personnalisées que vous avez créées :

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Compteurs de performances dans Analytics
Vous pouvez rechercher et afficher des rapports de compteur de performances dans [Analytics](../../azure-monitor/app/analytics.md).

Le schéma **compteur de performances** expose les noms `category`, `counter` et `instance` nom de chaque compteur de performance.  Dans les données de télémétrie de chaque application, vous voyez uniquement les compteurs de cette application. Par exemple, pour voir les compteurs disponibles : 

![Compteurs de performances dans Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(« Instance » fait ici référence à l’instance de compteur de performances, pas à l’instance de rôle ou de machine serveur. Le nom d’instance de compteur de performances segmente généralement les compteurs comme le temps processeur par le nom du processus ou de l’application.)

Pour obtenir un graphique présentant la mémoire disponible sur une période récente : 

![Graphique temporel dans Application Insights Analytics](./media/performance-counters/analytics-available-memory.png)

Comme les autres données de télémétrie, **performanceCounters** possède également une colonne `cloud_RoleInstance` qui indique l’identité de l’instance de serveur hôte sur lequel votre application est en cours d’exécution. Par exemple, pour comparer les performances de votre application sur des ordinateurs différents : 

![Performances segmentées par instances de rôle d’Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Nombres ASP.NET et Application Insights
*Quelle est la différence entre le taux d’exceptions et les mesures d’exceptions ?*

* *taux d’exceptions* est un compteur de performances système. Le CLR compte l’ensemble des exceptions gérées et non gérées qui sont levées et divise le total d’un intervalle d'échantillonnage par la longueur de cet intervalle. Le Kit de développement logiciel (SDK) Application Insights collecte ce résultat et l’envoie au portail.

* *Exceptions* représente le nombre de rapports TrackException reçus par le portail au cours de l’intervalle d’échantillonnage du graphique. Il comprend uniquement les exceptions gérées pour lesquelles vous avez écrit des appels TrackException dans votre code et n’inclut pas toutes les [exceptions non gérées](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Compteurs de performances dans les applications ASP.NET Core
Les compteurs de performances ne sont pris en charge que si l’application cible le .NET Framework complet. Il n’existe aucune possibilité de collecter les compteurs de performances pour les applications .NET Core.

## <a name="alerts"></a>Alertes
Comme d’autres mesures, vous pouvez [définir une alerte](../../azure-monitor/app/alerts.md) pour vous avertir si un compteur de performances dépasse une limite que vous spécifiez. Ouvrez le volet Alertes et cliquez sur Ajouter une alerte.

## <a name="next"></a>Étapes suivantes
* [Suivi des dépendances](../../azure-monitor/app/asp-net-dependencies.md)
* [Suivi des exceptions](../../azure-monitor/app/asp-net-exceptions.md)


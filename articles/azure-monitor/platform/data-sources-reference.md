---
title: Sources de données d’analyse dans Azure
description: En savoir plus sur toutes les sources de données d’analyse disponibles sur Azure dès aujourd'hui.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848540"
---
# <a name="consume-monitoring-data-from-azure"></a>Consommer des données d’analyse à partir d’Azure

Sur la plateforme Azure, nous rassemblons les données d’analyse dans un emplacement unique avec le pipeline Azure Monitor, mais sachez qu’aujourd'hui les données d’analyse ne sont pas encore toutes disponibles dans ce pipeline. Dans cet article, nous résumons les différentes façons dont vous pouvez accéder par programme aux données d’analyse à partir des services Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>Options pour la consommation de données

| Type de données | Catégorie | Services pris en charge | Méthodes d’accès |
| --- | --- | --- | --- |
| Mesures au niveau de la plateforme Azure Monitor | Mesures | [Consulter la liste ici](metrics-supported.md) | <ul><li>**API REST :** [API Métrique Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Objet blob de stockage ou hub d’événements :** [paramètres de diagnostic](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Mesures de SE invité Compute (ex. compteurs de performances) | Mesures | Machines virtuelles [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) et Linux (v2), [Services cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Objet blob ou table de stockage :** [diagnostics Azure pour Windows ou Linux](diagnostics-extension-to-storage.md)</li><li>**Hub d’événements :** [diagnostics Azure pour Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Mesures personnalisées ou d’application | Mesures | Toute application instrumentée avec Application Insights | <ul><li>**API REST :** [API REST Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Métriques de stockage | Mesures | Stockage Azure | <ul><li>**Table de stockage :** [Analyse du stockage](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Données de facturation | Mesures | Tous les services Azure | <ul><li>**API REST :** [API Azure Resource Usage et RateCard](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Journal d’activité | Événements | Tous les services Azure | <ul><li>**API REST :** [API Événements Azure Monitor](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Objet blob de stockage ou hub d’événements :** [profil de journal](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Journaux de diagnostics Azure Monitor | Événements | [Consulter la liste ici](diagnostic-logs-schema.md) | <ul><li>**Objet blob de stockage ou hub d’événements :** [paramètres de diagnostic](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Journaux d’activité de SE invité Compute (ex. IIS, ETW, syslogs) | Événements | Machines virtuelles [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) et Linux (v2), [Services cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Objet blob ou table de stockage :** [diagnostics Azure pour Windows ou Linux](diagnostics-extension-to-storage.md)</li><li>**Hub d’événements :** [diagnostics Azure pour Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Journaux d’activité App Service | Événements | App Services | <ul><li>**Fichier, table ou stockage d’objets blob :** [diagnostics de l’application web](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Journaux d’activité de stockage | Événements | Stockage Azure | <ul><li>**Table de stockage :** [Analyse du stockage](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertes Security Center | Événements | Azure Security Center | <ul><li>**API REST :** [alertes de sécurité](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Création de rapports Active Directory | Événements | Azure Active Directory | <ul><li>**API REST :** [API Graph Azure Active Directory](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| État des ressources de Security Center | Statut | [Toutes les ressources prises en charge](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST :** [états de sécurité](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Intégrité des ressources | Statut | Services pris en charge | <ul><li>**API REST :** [API REST Resource Health](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertes de métrique Azure Monitor | Notifications | [Consulter la liste ici](metrics-supported.md) | <ul><li>**Webhook :** [alertes de métrique Azure](alerts-webhooks.md)</li></ul> |
| Alertes Journal d’activité Azure Monitor | Notifications | Tous les services Azure | <ul><li>**Webhook :** alertes de journal d’activité Azure</li></ul> |
| Notifications de mise à l’échelle automatique | Notifications | [Consulter la liste ici](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook :** [schéma de la charge utile du webhook de notification de mise à l’échelle automatique](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertes Requête de recherche dans les journaux | Notifications | Journaux d’activité Azure Monitor | <ul><li>**Webhook :** [action webhook pour les règles d’alerte de journal](alerts-log-webhook.md)</li></ul> |
| Alertes de métrique Application Insights | Notifications | Application Insights | <ul><li>**Webhook :** [alertes Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |
| Tests web Application Insights | Notifications | Application Insights | <ul><li>**Webhook :** [alertes Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [mesures Azure Monitor](data-platform.md)
- En savoir plus sur le [journal d’activité Azure](activity-logs-overview.md)
- En savoir plus sur les [journaux de diagnostic Azure](diagnostic-logs-overview.md)


---
title: Personnaliser les règles de pare-feu d’applications web dans Azure Application Gateway - portail Azure
description: Cet article fournit des informations sur la personnalisation des règles de pare-feu d’applications web dans Application Gateway avec le portail Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: b18c9666e58925746a3b61740db6fb5118c2010b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733714"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personnaliser les règles de pare-feu d’applications web via le portail Azure

Le pare-feu d’applications web (WAF) Azure Application Gateway fournit une protection pour les applications web. Ces protections sont fournies par le jeu de règles (Core Rule Set, CRS) de l’Open Web Application Security Project (OWASP). Certaines règles peuvent entraîner des faux positifs et bloquer le trafic réel. Par conséquent, Application Gateway permet de personnaliser des règles et des groupes de règles. Pour plus d’informations sur les règles et groupes de règles spécifiques, consultez la [List of web application firewall CRS rule groups and rules](application-gateway-crs-rulegroups-rules.md) (Liste de règles et groupes de règles CRS de pare-feu d’applications web).

>[!NOTE]
> Si votre passerelle Application Gateway n’utilise pas la couche WAF, l’option de mise à niveau de la passerelle Application Gateway vers la couche WAF s’affiche dans le volet de droite. 

![Activer WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Afficher les règles et groupes de règles

**Pour afficher les règles et groupes de règles**
   1. Accédez à la passerelle Application Gateway, puis sélectionnez **Pare-feu d’applications web**.  
   2. Sélectionnez **Configuration de règle avancée**.  
   Un tableau s’affiche sur la page de tous les groupes de règles fournis avec l’ensemble de règles choisi. Toutes les cases à cocher de la règle sont sélectionnées.

![Configurer des règles désactivées][1]

## <a name="search-for-rules-to-disable"></a>Rechercher des règles à désactiver

Le panneau des **paramètres de pare-feu d’applications web** permet de filtrer les règles à l’aide d’une recherche de texte. Le résultat affiche uniquement les groupes de règles et les règles contenant le texte que vous avez recherché.

![Rechercher des règles][2]

## <a name="disable-rule-groups-and-rules"></a>Désactiver les règles et les groupes de règles

Lorsque vous désactivez des règles, vous pouvez désactiver un groupe de règles entier ou des règles spécifiques sous un ou plusieurs groupes de règles. 

**Pour désactiver des groupes de règles ou des règles spécifiques**

   1. Recherchez les règles ou les groupes de règles que vous voulez désactiver.
   2. Désactivez les cases à cocher correspondant aux règles que vous voulez désactiver. 
   2. Sélectionnez **Enregistrer**. 

![Enregistrer les modifications][3]

## <a name="mandatory-rules"></a>Règles obligatoires

La liste suivante contient des conditions qui entraînent le WAF bloquer la demande en mode de prévention (en Mode de détection ils sont enregistrés en tant qu’exceptions). Il ne peut pas être configurées ou désactivés :

* Échec d’analyser le corps de la requête entraîne la demande est bloquée, à moins que l’inspection du corps est mis sous tension (XML, JSON, les données de formulaire)
* Longueur de données de corps (avec aucun fichier) de la demande est supérieure à la limite configurée
* Demande de corps (y compris les fichiers) est supérieure à la limite
* Une erreur interne s’est produite dans le moteur de WAF

CRS 3.x spécifiques :

* Trafic entrant de seuil du score dépassé d’anomalie

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré vos règles désactivées, vous pouvez apprendre à afficher vos journaux d’activité WAF. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

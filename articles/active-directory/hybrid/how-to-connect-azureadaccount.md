---
title: 'Synchronisation d’Azure AD Connect : Comment gérer le compte de service Azure AD | Microsoft Docs'
description: Cette rubrique explique comment restaurer le compte de service Azure AD.
services: active-directory
keywords: AADSTS70002, AADSTS50054, Comment réinitialiser le mot de passe du compte du service Connecteur de synchronisation Azure AD Connect
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: f88318c87e29567b40b5eacf10f3b6f259adee8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351004"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Synchronisation d’Azure AD Connect : Comment gérer le compte de service Azure AD
Le compte de service utilisé par le connecteur Azure AD est censé être proposé en libre-service. Si vous devez réinitialiser les informations d’identification, cette rubrique vous concerne. Par exemple, un administrateur général peut avoir réinitialisé par erreur le mot de passe du compte de service à l’aide de PowerShell.

## <a name="reset-the-credentials"></a>Réinitialisation des informations d'identification
Si le compte de service défini sur le Connecteur Azure AD ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure AD Connect et démarrez PowerShell.
2. Exécutez `Add-ADSyncAADServiceAccount`.  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournissez les informations d’identification de l’administrateur général Azure AD.

Cette applet de commande réinitialise le mot de passe du compte de service et l’actualise dans Azure AD et dans le moteur de synchronisation.

## <a name="known-issues-these-steps-can-solve"></a>Problèmes connus pouvant être résolus par les procédures indiquées ci-après
Cette section est une liste d’erreurs signalées par les clients qui ont été résolues par une réinitialisation des informations d’identification sur le compte de service Azure AD.

- - -
Événement 6900  
Le serveur a rencontré une erreur inattendue lors du traitement d’une notification de modification de mot de passe :  
AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

- - -
Événement 659  
Erreur lors de la récupération de la configuration de la synchronisation de stratégie de mot de passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException :  
AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

## <a name="next-steps"></a>Étapes suivantes
**Rubriques de présentation**

- [Synchronisation Azure AD Connect : Comprendre et personnaliser la synchronisation](how-to-connect-sync-whatis.md)
- [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)



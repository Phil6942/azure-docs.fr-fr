---
title: Définir des attributs personnalisés dans Azure Active Directory B2C | Microsoft Docs
description: Définissez des attributs personnalisés pour votre application dans Azure Active Directory B2C afin de recueillir des informations sur vos clients.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 277a1d5df0e6eb828c4d3c6faed91d55defb4108
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317389"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Définir des attributs personnalisés dans Azure Active Directory B2C

 Toutes les applications orientées client ont des exigences uniques concernant les informations qui doivent être recueillies. Votre locataire Azure Active Directory (Azure AD) B2C est fourni avec un ensemble intégré d’informations stockées dans des attributs : le prénom, le nom, la ville et le code postal. Avec Azure AD B2C, vous pouvez étendre l’ensemble d’attributs stockés sur chaque compte client. 
 
 Vous pouvez créer des attributs personnalisés dans le [portail Azure](https://portal.azure.com/) et les utiliser dans vos flux d’utilisateur d’inscription, de connexion ou de modification de profil. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md). Les attributs personnalisés dans Azure AD B2C utilisent les [Extensions de schéma de répertoire de l’API Azure AD Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Création d’un attribut personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Sélectionnez **Attributs utilisateur**, puis **Ajouter**.
5. Fournissez un **nom** pour l’attribut personnalisé (par exemple, « ShoeSize »).
6. Choisissez un **Type de données**. Seules les valeurs **String**, **Boolean** et **Int** sont disponibles.
7. Si vous le souhaitez, entrez une **Description** à titre d’information. 
8. Cliquez sur **Créer**.

L’attribut personnalisé est actuellement disponible dans la liste des **attributs utilisateur**, et pour une utilisation dans vos flux d’utilisateur. Un attribut personnalisé est créé lors de sa première utilisation dans un flux d’utilisateur, et non pas quand vous l’ajoutez à la liste des **Attributs utilisateur**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Utilisation d’un attribut personnalisé dans votre flux d’utilisateur

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
2. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir. 
4. Sélectionnez **Attributs d’utilisateur**, puis sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **Enregistrer**.
5. Sélectionnez **Revendications d’applications**, puis sélectionnez l’attribut personnalisé. 
6. Cliquez sur **Enregistrer**.

La fonctionnalité **Exécuter le flux d’utilisateur** du flux d’utilisateur permet de vérifier l’expérience client. Vous devez maintenant voir **ShoeSize** dans la liste d’attributs collectés lors de l’inscription, et le voir dans le jeton retourné à votre application.


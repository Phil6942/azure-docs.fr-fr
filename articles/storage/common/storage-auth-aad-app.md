---
title: Authentification avec Azure Active Directory pour accéder aux objets blob et file d’attente des données à partir de vos applications | Microsoft Docs
description: Utilisez Azure Active Directory pour s’authentifier à partir d’une application et autoriser des demandes aux objets BLOB et files d’attente.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c4959d6abd1b33c81f731ad0479bd59d271c7797
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149161"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Authentification avec Azure Active Directory à partir d’une application pour l’accès aux objets BLOB et files d’attente

Le principal avantage d’utiliser Azure Active Directory (Azure AD) avec le Stockage Azure est que vos informations d’identification n’ont plus besoin d’être stockées dans votre code. À la place, vous pouvez demander un jeton d’accès OAuth 2.0 à Azure AD. Azure AD gère l’authentification du principal de sécurité (un utilisateur, un groupe ou un principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD retourne le jeton d’accès à l’application et l’application peut ensuite l’utiliser pour autoriser les demandes vers le Stockage Azure.

Cet article décrit comment configurer votre application pour l’authentification avec Azure AD. L’exemple de code utilise .NET, mais l’approche est similaire avec d’autres langages.

Avant de vous authentifier un principal de sécurité à partir de votre application Azure Storage, configurer les paramètres de contrôle (RBAC) d’accès en fonction du rôle pour ce principal de sécurité. Le Stockage Azure définit des rôles RBAC qui englobent les autorisations pour les conteneurs et les files d’attente. Quand le rôle RBAC est attribué à un principal de sécurité, ce dernier obtient l’accès à cette ressource. Pour plus d’informations, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).

Pour avoir une vue d’ensemble du flux d’octroi de code OAuth 2.0, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

Pour autoriser les opérations d’objet blob et de file d’attente avec un jeton OAuth, vous devez utiliser HTTPS.

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Attribuer un rôle RBAC à un principal de sécurité Azure AD

Pour authentifier un principal de sécurité à partir de votre application Stockage Azure, commencez par configurer les paramètres de contrôle d’accès en fonction du rôle (RBAC) pour ce principal de sécurité. Le Stockage Azure définit des rôles RBAC qui englobent les autorisations pour les conteneurs et les files d’attente. Quand le rôle RBAC est attribué à un principal de sécurité, ce dernier obtient l’accès à cette ressource. Pour plus d’informations, consultez [gérer les droits d’accès aux données d’objets Blob Azure et de file d’attente avec RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Inscrire votre application à un locataire Azure AD

La première étape d’utilisation d’Azure AD pour autoriser l’accès aux ressources de stockage est d’inscrire votre application cliente dans un locataire Azure AD. L’inscription de votre application vous permet d’appeler la [Bibliothèque d’authentification Active Directory](../../active-directory/active-directory-authentication-libraries.md) (ADAL) Azure à partir de votre code. La bibliothèque ADAL fournit une API pour l’authentification avec Azure AD à partir de votre application. L’inscription de votre application vous permet aussi d’autoriser les appels de cette application aux API de Stockage Azure avec un jeton d’accès.

Lorsque vous inscrivez votre application, vous fournissez des informations sur votre application à Azure AD. Azure AD fournit ensuite un ID de client (appelé aussi *ID d’application*) que vous utilisez pour associer votre application à Azure AD au moment de l’exécution. Pour en savoir plus sur l’ID de client, consultez [Objets application et principal du service dans Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pour inscrire votre application de Stockage Azure, suivez les étapes de la section [Ajout d’une application](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) dans [Intégration d’applications dans Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Si vous inscrivez votre application comme une application native, vous pouvez spécifier n’importe quel URI valide pour **l’URI de redirection**. La valeur ne doit pas forcément être un point de terminaison réel.

![Capture d’écran montrant comment inscrire votre application de stockage avec Azure AD](./media/storage-auth-aad-app/app-registration.png)

Une fois votre application inscrite, l’ID d’application (ou ID de client) se trouve sous **Paramètres** :

![Capture d’écran montrant l’ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Pour plus d’informations sur l’inscription d’une application dans Azure AD, consultez [Intégration d’applications à Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Accorder à votre application inscrite des autorisations sur le Stockage Azure

Ensuite, vous devez accorder à votre application l’autorisation d’appeler les API de Stockage Azure. Cette étape permet à votre application d’autoriser les appels au Stockage Azure avec Azure AD.

1. Dans le volet de navigation de gauche du portail Azure, choisissez **Tous les services** et recherchez **Inscriptions des applications**.
2. Recherchez le nom de l’application inscrite que vous avez créée à l’étape précédente.
3. Sélectionnez votre application inscrite et cliquez sur **Paramètres**. Dans la section **Accès API**, sélectionnez **Autorisations requises**.
4. Dans le panneau **Autorisations requises**, cliquez sur le bouton **Ajouter**.
5. Sous **Sélectionner une API**, recherchez « Stockage Azure », puis sélectionnez **Stockage Azure** dans la liste des résultats.

    ![Capture d’écran montrant les autorisations pour le stockage](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Sous **Sélectionner des autorisations**, cochez la case à côté de **Accéder au Stockage Azure**, puis cliquez sur **Sélectionner**.
7. Cliquez sur **Done**.

La fenêtre **Autorisations nécessaires** indique à présent que votre application Azure AD a accès à Azure Active Directory et au Stockage Azure. Les autorisations sont accordées à Azure AD automatiquement quand vous inscrivez votre application pour la première fois dans Azure AD.

![Capture d’écran montrant inscrire les autorisations d’application](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : Créer un objet blob de blocs

L’exemple de code montre comment obtenir un jeton d’accès d’Azure AD. Le jeton d’accès est utilisé pour authentifier l’utilisateur spécifié et autoriser une demande de création d’objet blob de blocs. Pour que cet exemple fonctionne, suivez d’abord les étapes décrites dans les sections précédentes.

> [!NOTE]
> En tant que propriétaire de votre compte de stockage Azure, vous n’avez pas automatiquement l’autorisation d’accéder aux données. Vous devez explicitement vous attribuer un rôle RBAC pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage, conteneur ou file d’attente. 
>
> Par exemple, pour exécuter l’exemple de code sur un compte de stockage dont vous êtes propriétaire et sous votre propre identité d’utilisateur, vous devez vous attribuer le rôle RBAC Contributeur aux données blob. Sinon, l’appel pour créer l’objet blob échoue avec le code d’état HTTP 403 (Interdit). Pour plus d’informations, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valeurs connues pour l’authentification avec Azure AD

Pour authentifier un principal de sécurité avec Azure AD, vous devez inclure des valeurs connues dans votre code.

#### <a name="azure-ad-authority"></a>Autorité Azure AD

Pour le cloud public Microsoft, l’autorité Azure AD de base est comme suit, où *tenant-id* est votre ID de locataire Active Directory (ou ID de répertoire) :

`https://login.microsoftonline.com/<tenant-id>/`

L’ID client identifie le client Azure AD pour l’authentification. Pour récupérer l’ID de locataire, suivez les étapes décrites dans **Obtenir l’ID de locataire pour Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID de ressource de stockage

Utilisez l’ID de ressource de Stockage Azure pour obtenir un jeton d’authentification des demandes sur le Stockage Azure :

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtenir l’ID client pour votre Azure Active Directory

Pour obtenir l’ID client, procédez comme suit :

1. Dans le portail Azure, sélectionnez votre Active Directory.
2. Cliquez sur **Propriétés**.
3. Copiez la valeur GUID fournie pour l’**ID de répertoire**. Cette valeur est également appelée l’ID client.

![Capture d’écran montrant comment copier l’ID de locataire](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Ajouter des références et des instructions using  

À partir de Visual Studio, installez la bibliothèque cliente Azure Storage. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**. Tapez la commande suivante dans la console pour installer la dernière version de la bibliothèque cliente pour .NET :

```
Install-Package WindowsAzure.Storage
```

Installez également la dernière version d’ADAL :

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Ensuite, ajoutez les instructions using suivantes dans votre code :

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Obtenir un jeton OAuth d’Azure AD

Ensuite, ajoutez une méthode qui demande un jeton à Azure AD. Pour demander le jeton, appelez la méthode [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync). Veillez à avoir les valeurs suivantes provenant des étapes suivies précédemment :

- ID de locataire (annuaire)
- ID de client (application)
- URI de redirection du client

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Créer l’objet blob de blocs

Enfin, utilisez le jeton d’accès pour créer de nouvelles informations d’identification de stockage et utiliser ces informations d’identification pour créer l’objet blob. N’oubliez pas que pour autoriser les opérations d’objet blob et file d’attente avec un jeton OAuth, vous devez utiliser HTTPS. :

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> L’intégration d’Azure AD au Stockage Azure nécessite l’utilisation du protocole HTTPS pour les opérations relatives au Stockage Azure.

Dans l’exemple ci-dessus, la bibliothèque cliente .NET gère l’autorisation de la demande de création de l’objet blob de blocs. D’autres bibliothèques clientes de stockage gèrent également l’autorisation de la demande pour vous. Toutefois, si vous appelez une opération Stockage Azure avec un jeton OAuth à l’aide de l’API REST, puis vous devez autoriser la demande en utilisant le jeton OAuth.   

Pour appeler les opérations des services Blob et de File d’attente à l’aide des jetons d’accès OAuth, passez le jeton d’accès dans l’en-tête **Authorization** en utilisant le schéma **Bearer** et spécifiez une version de service 2017-11-09 ou ultérieure, comme indiqué dans l’exemple suivant :

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Pour plus d’informations sur l’autorisation d’opérations de stockage Azure à partir de REST, consultez [s’authentifier avec Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour en savoir plus sur l’utilisation des identités gérées pour les ressources Azure avec le stockage Azure, consultez [authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md).
- Pour savoir comment exécuter des commandes Azure CLI et PowerShell avec les informations d’identification Azure AD, consultez [exécuter Azure CLI ou PowerShell des commandes avec informations d’identification Azure AD pour accéder aux données d’objet blob ou file d’attente](storage-auth-aad-script.md).

---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531099"
---
Vous pouvez acheter des domaines dans le [portail de gestion Azure](https://portal.azure.com) directement. Procédez comme suit pour acheter des noms de domaine et les attribuer à votre application web.

1. Dans votre navigateur, ouvrez le [portail de gestion Azure](https://portal.azure.com).
2. Sous l’onglet **Web Apps**, cliquez sur le nom de votre application web et sélectionnez **Paramètres**, puis **Domaines personnalisés et SSL**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Dans le volet **Domaines personnalisés et SSL**, cliquez sur **Acheter des domaines**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. Dans le volet **Acheter des domaines**, utilisez la zone de texte pour saisir le nom de domaine que vous voulez acheter. Les domaines disponibles proposés s'affichent simplement dans la zone de texte. Sélectionnez le domaine que vous souhaitez acheter.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Cliquez sur **Informations de contact** et remplissez le formulaire d’informations de contact du domaine.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Cliquez sur **Sélectionner** dans le panneau **Acheter des domaines**. Les informations d’achat s’affichent dans le panneau **Confirmation d’achat**. Si vous acceptez les conditions juridiques et cliquez sur **Acheter**, votre commande est envoyée et vous pouvez surveiller le processus d’achat dans **Notification**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Si vous avez correctement commandé un domaine, vous pouvez gérer le domaine et l'attribuer à votre application web. Cliquez sur **…**, à droite de votre domaine. Vous pouvez **Annuler l’achat** ou **Gérer le domaine**. Cliquez sur **Gérer le domaine**. Vous pouvez lier un **sous-domaine** à votre application web dans le panneau **Gérer le domaine**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **Liaisons de nom d’hôte** de votre application web.

À ce stade, vous devez pouvoir entrer le nom de domaine personnalisé dans votre navigateur et vérifier que vous êtes bien dirigé vers votre application web.


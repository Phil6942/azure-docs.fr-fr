---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522521"
---
## <a name="validate-container-is-running"></a>Valider conteneur est en cours d’exécution 

Il existe plusieurs façons de valider le conteneur est en cours d’exécution : 

|Requête|Objectif|
|--|--|
|`http://localhost:5000/`|Le conteneur fournit une page d’accueil.|
|`http://localhost:5000/status`|Demandé par GET, pour valider le conteneur est en cours d’exécution sans provoquer d’une requête de point de terminaison. Cela peut être utilisé pour Kubernetes [sondes de temporisation et de préparation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Le conteneur fournit un ensemble complet de documentation pour les points de terminaison ainsi qu’une fonctionnalité `Try it now`. Cette fonctionnalité vous permet d’entrer vos paramètres dans un formulaire HTML basé sur le web et d’effectuer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer le format du corps et des en-têtes HTTP requis. |

![Page d’accueil du conteneur](./media/cognitive-services-containers-api-documentation/container-webpage.png)

---
title: Ajouter un service à l’interface utilisateur de la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article vous montre comment ajouter un nouveau service à l’interface utilisateur web de l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094505"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de la solution de supervision à distance

Cet article vous montre comment ajouter un nouveau service à l’interface utilisateur web de l’accélérateur de solution de supervision à distance. Cet article décrit :

- Comment préparer un environnement de développement local.
- Comment ajouter un nouveau service à l’interface utilisateur web.

L’exemple de service de cet article fournit les données d’une grille que l’article pratique [Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-grid.md) vous montre comment ajouter.

Dans une application React, un service interagit généralement avec un service back-end. Les exemples de l’accélérateur de solution de supervision à distance comptent des services qui interagissent avec le responsable du hub IoT et les microservices de configuration.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce guide pratique, vous devez avoir installé les logiciels suivants sur votre machine de développement locale :

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>Avant de commencer

Vous devez effectuer les étapes décrites dans le guide pratique [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md) avant de continuer.

## <a name="add-a-service"></a>Ajouter un service

Pour ajouter un service à l’interface utilisateur web, vous devez ajouter les fichiers sources qui définissent le service et modifier certains fichiers existants pour que l’interface utilisateur web ait connaissance du nouveau service.

### <a name="add-the-new-files-that-define-the-service"></a>Ajouter les nouveaux fichiers qui définissent le service

Pour vous aider à démarrer, le dossier **src/walkthrough/services** contient les fichiers qui définissent un service simple :

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Pour en savoir plus sur la façon dont les services sont implémentés, consultez [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Copiez **exampleService.js** dans le dossier **src/services**, puis copiez **exampleModels.js** dans le dossier **src/services/models**.

Mettez à jour le fichier **index.js** dans le dossier **src/services** pour exporter le nouveau service :

```js
export * from './exampleService';
```

Mettez à jour le fichier **index.js** dans le dossier **src/services/models** pour exporter le nouveau modèle :

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurer les appels au service à partir du magasin

Pour vous aider à démarrer, le dossier **src/walkthrough/store/reducers** contient un exemple de « reducer » :

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Copiez **exampleReducer.js** dans le dossier **src/store/reducers**.

Pour en savoir plus sur le « reducer » et les **épopées**, consultez [redux observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configurer les middlewares

Pour configurer les middlewares, ajoutez le « reducer » dans le fichier **rootReducer.js** du dossier **src/store** :

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Ajoutez les épopées dans le fichier **rootEpics.js** du dossier **src/store** :

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour ajouter ou personnaliser des services dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance.

Maintenant que vous avez défini un service, l’étape suivante consiste à [Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-grid.md) qui affiche les données retournées par le service.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md).

---
title: Comportement d’appareil simulé dans la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article explique comment utiliser JavaScript pour définir le comportement d’un appareil simulé dans la solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449518"
---
# <a name="implement-the-device-model-behavior"></a>Implémenter le comportement de modèle d’appareil

L’article [Comprendre le schéma de modèle d’appareil](iot-accelerators-remote-monitoring-device-schema.md) décrit le schéma qui définit un modèle d’appareil simulé. Cet article s’appuie sur deux types de fichiers JavaScript qui implémentent le comportement d’un appareil simulé :

- Les fichiers JavaScript d’**état**, qui s’exécutent à intervalle régulier pour mettre à jour l’état interne de l’appareil.
- Les fichiers JavaScript de **méthode**, qui s’exécutent lorsque la solution appelle une méthode sur l’appareil.

> [!NOTE]
> Les comportements de modèle d’appareil sont destinés uniquement aux appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Dans cet article, vous apprendrez comment :

>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé répond à un appel de méthode à partir de la solution de supervision à distance
> * Déboguer vos scripts

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a décrit comment définir le comportement de votre propre modèle personnalisé d’appareil simulé. Cet article vous a montré comment :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé répond à un appel de méthode à partir de la solution de supervision à distance
> * Déboguer vos scripts

Maintenant que vous avez découvert comment spécifier le comportement d’un appareil simulé, nous vous recommandons d’apprendre à [Créer un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

Pour plus d’informations sur le développement de la solution de supervision à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->

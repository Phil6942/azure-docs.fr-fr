---
title: Entités de données | Microsoft Docs
description: Une vue d’ensemble des entités de données.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397292"
---
# <a name="data-entities"></a>Entités de données

Cet article définit et fournit une vue d’ensemble des entités de données. Il fournit des informations sur les fonctionnalités des entités de données, les scénarios qu’ils prennent en charge, les catégories utilisées, ainsi que les méthodes permettant de les créer.

## <a name="overview"></a>Vue d'ensemble

Une entité de données est une abstraction de l’implémentation physique des tables de base de données. Par exemple, dans des tables normalisées, une grande partie des données de chaque client peut être stockée dans une table client, et le reste peut-être réparti sur un petit ensemble de tables associées. Dans ce cas, l’entité de données pour le concept de client apparaît sous la forme d’une vue dénormalisée, dans laquelle chaque ligne contient toutes les données de la table client et de ses tables associées. Une entité de données encapsule un concept métier dans un format qui facilite le développement et l’intégration. La nature abstraite d’une entité de données peut simplifier le développement et la personnalisation d’applications. Par la suite, l’abstraction isole également code d’application de l’activité inévitable des tables physiques entre les versions.

Pour résumer : l’entité de données fournit l’abstraction et l’encapsulation (vue dénormalisée) conceptuelles des schémas de table sous-jacents afin de représenter les concepts et fonctionnalités de données clés.

## <a name="capabilities"></a>Fonctionnalités

Une entité de données intègre les fonctionnalités suivantes :

- Elle remplace les concepts divergents et fragmentés d’entités AXD, DIXF (Data Import/Export Framework) et de requêtes agrégées par un concept unique.
- Elle fournit une pile unique pour capturer la logique métier et pour permettre des scénarios tels que l’importation/exportation, l’intégration et la programmabilité.
- Elle devient le mécanisme principal d’exportation et d’importation de packages de données pour les scénarios de données Application Lifecycle Management (ALM) et de démonstration.
- Elle peut être exposée en tant que services OData, puis utilisée dans des scénarios d’intégration synchrone de style tabulaire et des intégrations Microsoft Office.

Consultez [Entités de données](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) pour plus d’informations.

---
title: Autorités de certification autorisées pour le protocole HTTPS personnalisé sur Azure CDN | Microsoft Docs
description: Si vous utilisez votre propre certificat pour activer HTTPS sur un domaine personnalisé, vous devrez utiliser une autorité de certification autorisée (CA) pour le créer.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323516"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorités de certification autorisées pour le protocole HTTPS personnalisé sur Azure CDN

Pour un domaine personnalisé Azure CDN (Content Delivery Network) sur un point de terminaison **Azure CDN Standard de Microsoft**, lorsque vous [activez la fonctionnalité HTTPS à l’aide de votre propre certificat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), vous devez utiliser une autorité de certification autorisée pour créer votre certificat SSL. Si vous utilisez une autorité de certification non autorisée ou un certificat auto-signé, votre demande sera rejetée.

> [!NOTE]
> Seuls les profils **Azure CDN Standard de Microsoft** permettent d’utiliser son propre certificat pour activer le protocole HTTPS personnalisé. 
>

Les autorités de certification suivantes sont autorisées pour créer son propre certificat :

- AddTrust External CA Root
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 Extended Validation Server CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Root CA X3
- D-trust Root Class 3 CA 2 2009
- Encryption Everywhere DV TLS CA
- Entrust Root Certification Authority
- Entrust Root Certification Authority - G2
- Entrust.net Certification Authority (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA – SHA256 – G2
- GlobalSign Organization Validation CA – G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority - G2
- Go Daddy Secure Certificate Authority - G2
- QuoVadis Root CA2 G3
- RapidSSL RSA CA 2018
- Symantec Class 3 EV SSL CA – G3
- Symantec Class 3 Secure Server CA – G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA - G2
- Thawte Primary Root CA - G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority – G5
- VeriSign International Server CA – Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority



---
title: Chaud, froid et archive les niveaux d’accès pour les objets BLOB - stockage Azure
description: Chaud, froid et archive les niveaux d’accès pour les comptes de stockage Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 9be62e09fbf65081f166c89f5358cdb6677c22c6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004225"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Stockage d’objets Blob Azure : chaud, froid et archive les niveaux d’accès

Stockage Azure offre des niveaux d’accès différents, ce qui vous permet de stocker des données d’objet blob de la manière la plus rentable. Les niveaux d’accès disponibles sont les suivantes :

- **Chaud** - optimisé pour stocker les données fréquemment sollicitées.
- **Froid** - optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 30 jours.
- **Archive** - optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 180 jours avec les conditions de latence flexibles (selon l’ordre des heures).

Les considérations suivantes s’appliquent pour les niveaux d’accès différents :

- Le niveau d’accès archive est disponible uniquement au niveau du blob et non au niveau du compte de stockage.
- Données dans le niveau d’accès froid peuvent tolérer une disponibilité légèrement inférieure, mais nécessite toujours une durabilité élevée et des caractéristiques de temps d’accès et le débit similaires en tant que données chaudes. Pour les données froides, un contrat de niveau de service de disponibilité légèrement inférieure (SLA) et l’accès supérieurs les coûts par rapport aux données chaudes sont des compromis acceptables pour réduire les coûts de stockage.
- Le stockage archive est hors connexion et offre les coûts de stockage les plus bas, mais également les coûts d’accès les plus élevés.
- Uniquement les niveaux d’accès chaud et froid peuvent être définis au niveau du compte.
- Chaud, froid et archive niveaux peuvent indiquer au niveau de l’objet.

Les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue pour optimiser les coûts. Les données stockées dans le cloud peuvent être différentes en termes de comment il a généré, traitées et accessible via sa durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. Certaines données restent inactives dans le cloud et est rarement, si sollicitées une fois qu’il est stocké.

Chacun des scénarios d’accès aux données peut bénéficier des avantages d’un niveau d’accès différent est optimisé pour un modèle d’accès particulier. Avec chaud, froid et niveaux d’accès archive, adresses de stockage Blob Azure séparer ce besoin de niveaux d’accès différencié avec les modèles de tarification.

## <a name="storage-accounts-that-support-tiering"></a>Comptes de stockage prenant en charge la hiérarchisation

Vous pouvez uniquement hiérarchiser vos données de stockage d’objet sur chaud, froid ou archive dans le stockage d’objets Blob et usage général v2 (comptes GPv2). Les comptes Usage général v1 (GPv1) ne prennent pas en charge la hiérarchisation. Toutefois, vous pouvez facilement convertir les comptes de stockage GPv1 ou d’objets Blob existants pour les comptes GPv2 en un clic dans le portail Azure. GPv2 fournit une nouvelle structure de tarification pour les objets BLOB, fichiers, files d’attente et des accès à une variété de nouvelles fonctionnalités de stockage. À l’avenir, certains morceaux de prix et de fonctionnalités nouvelles est proposés uniquement les comptes GPv2. Par conséquent, vous devez évaluer à l’aide de comptes GPv2, mais ne les utilisent qu’après avoir examiné la tarification pour tous les services. Certaines charges de travail peuvent être plus chères sur GPv2 que sur GPv1. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

Stockage BLOB et exposer des comptes GPv2 le **niveau d’accès** attribut au niveau du compte. Cet attribut vous permet de spécifier le niveau d’accès par défaut en tant que chaud ou froid pour tout objet blob dans le compte de stockage qui n’a pas aucun niveau explicitement défini au niveau de l’objet. Le niveau du compte ne s’applique pas aux objets disposant d’un niveau établi au niveau de l’objet. Le niveau archive peut être appliqué uniquement au niveau de l’objet. Vous pouvez basculer entre ces niveaux d’accès à tout moment.

## <a name="premium-performance-block-blob-storage"></a>Stockage blob par blocs performances Premium

Stockage blob par blocs performances Premium rend les données fréquemment sollicitées disponibles par le biais de matériel hautes performances. Données de ce niveau de performances sont stockées sur des disques SSD (SSD), qui sont optimisés pour une latence faible et cohérente. Les disques SSD offrent des taux transactionnelles plus élevés et débit par rapport aux disques durs classiques.

Stockage blob par blocs performances Premium est idéal pour les charges de travail qui nécessitent des temps de réponse rapide et cohérente. Il est préférable pour les charges de travail impliquant beaucoup de petites transactions, telles que la capture de données de télémétrie, la messagerie et la transformation des données. Les données qui implique des utilisateurs finaux, telles que le montage vidéo interactive, contenu web statique et transactions en ligne sont également de bons candidats.

Stockage blob par blocs performances Premium est uniquement disponible via un type de compte stockage blob de bloc et ne prend pas en charge la hiérarchisation pour chaud, froid ou archive des niveaux d’accès.

## <a name="hot-access-tier"></a>Niveau d’accès chaud

Le niveau d’accès chaud possède des coûts de stockage plus élevés que les niveaux froid et archive, mais les coûts d’accès la plus basse. Exemples de scénarios d’utilisation pour le niveau d’accès à chaud sont les suivantes :

- Données qui sont en cours d’utilisation ou censé être sollicitées (lecture à partir d’et écriture).
- Données sont conservées pour traitement et migration éventuelle vers le niveau d’accès froid.

## <a name="cool-access-tier"></a>Niveau d’accès froid

Le niveau d’accès froid a la réduction des coûts de stockage et les coûts d’accès supérieurs par rapport au stockage chaud. Ce niveau est prévu pour les données qui resteront dans le niveau froid pendant au moins 30 jours. Exemples de scénarios d’utilisation pour le niveau d’accès froid sont les suivantes :

- Sauvegarde à court terme et récupération d’urgence de jeux de données.
- Ancien contenu multimédia qui n’est plus consulté fréquemment mais qui est censé être disponible immédiatement lors d’un accès.
- Jeux de données volumineux qui doivent être stockés de manière économique tout en permettant la collecte de plus de données à des fins de traitement ultérieur. (*Par exemple*, le stockage à long terme de données scientifiques, les données de télémétrie brute d’un site de production)

## <a name="archive-access-tier"></a>Niveau d’accès archive

Le niveau d’accès archive a le plus faible coût de stockage et les coûts plus élevés de l’extraction de données par rapport aux niveaux chaud et froid. Ce niveau est destiné aux données qui peuvent tolérer plusieurs heures de latence de récupération et restant dans le niveau archive pendant au moins 180 jours.

Bien qu’il soit un objet blob dans stockage archive, les données blob sont hors connexion et ne peuvent pas être lues, copiées, remplacées ou modifiées. Vous ne pouvez pas effectuer des captures instantanées d’un objet blob dans stockage archive. Toutefois, les métadonnées de l’objet blob restent en ligne et disponible, ce qui vous permet de répertorier l’objet blob et ses propriétés. Pour les objets blob dans l’archive, les seules opérations valides sont GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier et DeleteBlob.

Exemples de scénarios d’utilisation pour le niveau d’accès archive sont les suivantes :

- Sauvegarde à long terme, sauvegarde secondaire et jeux de données d’archivage
- Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable (*Par exemple*, des fichiers multimédias bruts après transcodage dans d’autres formats)
- Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées (*Par exemple*, séquences vidéo de sécurité, anciens clichés de radiographie pour les organismes de santé, les enregistrements audio et les transcriptions d'appel client pour les services financiers)

### <a name="blob-rehydration"></a>Rafraîchissement des objets blob

Pour lire des données dans le stockage archive, vous devez d’abord attribuer un niveau chaud ou froid à l’objet blob. Ce processus est appelé « réalimentation » et peut durer jusqu’à 15 heures. Les grandes tailles d’objets blob sont recommandées pour des performances optimales. La réalimentation simultanée de plusieurs petits objets blob peut demander du temps supplémentaire.

Pendant la réalimentation, vous pouvez consulter la propriété **État archive** de l’objet blob pour vous assurer que le niveau a changé. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété **Niveau d’accès** de l’objet blob indique le niveau chaud ou froid.  

## <a name="blob-level-tiering"></a>Hiérarchisation au niveau de l’objet blob

La hiérarchisation au niveau de l’objet blob vous permet de modifier le niveau de vos données au niveau de l’objet, à l’aide d’une seule opération nommée [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier). Vous pouvez facilement modifier le niveau d’accès (chaud, froid ou archive) d’un objet blob, comme si vous modifiez le mode d’utilisation, sans avoir à déplacer des données entre les comptes. Tous les changements de niveau prennent effet immédiatement. Toutefois, la réactivation d’un objet blob d’un niveau archive peut prendre plusieurs heures.

L’heure de la dernière modification du niveau de l’objet blob est exposée via la propriété de l’objet blob **Access Tier Change Time**. Si un objet blob est dans le niveau archive, il ne peut pas être remplacé, afin de charger le même objet blob n’est pas autorisée dans ce scénario. Vous pouvez remplacer un objet blob dans un niveau chaud ou froid, dans lequel cas le nouvel objet blob hérite le niveau de l’objet blob qui a été remplacé.

Objets BLOB de tous les niveaux d’accès de trois peuvent coexister dans le même compte. Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte. Si le niveau d’accès est déduit à partir du compte, vous voyez la propriété de l’objet blob **Niveau d’accès déduit** définie sur la valeur « True », et la propriété de l’objet blob **Niveau d’accès** correspond au niveau du compte. Dans le portail Azure, le niveau d’accès déduit la propriété est affichée avec le niveau d’accès de blob (par exemple, **chaud [déduit]** ou **froid [déduit]**).

> [!NOTE]
> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs. De plus, vous ne pouvez pas modifier le niveau d’un objet blob de blocs comportant des captures instantanées.

> [!NOTE]
> Données stockées dans un compte de stockage des objets blob de bloc ne peut pas actuellement être placé au niveau chaud, froid ou archive à l’aide de [définir le niveau du Blob](/rest/api/storageservices/set-blob-tier) ou à l’aide de la gestion du cycle de vie du stockage d’objets Blob Azure.
> Pour déplacer des données, vous devez synchrone copier des objets BLOB à partir du compte de stockage des objets blob de bloc pour le niveau d’accès à chaud dans un autre compte à l’aide du [placer le bloc à partir d’API URL](/rest/api/storageservices/put-block-from-url) ou une version d’AzCopy qui prend en charge de cette API.
> L’API *Put Block From URL* copie les données sur le serveur de manière synchrone, ce qui signifie que l’appel est effectué uniquement quand toutes les données ont été déplacées de l’emplacement d’origine sur le serveur vers l’emplacement de destination.

### <a name="blob-lifecycle-management"></a>Gestion de cycle de vie des objets blob

Gestion du cycle de vie du stockage BLOB offre une stratégie riche, basé sur la règle que vous pouvez utiliser pour la transition de vos données pour le meilleur niveau d’accès et faire expirer les données à la fin de son cycle de vie. Consultez [Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md) pour en savoir plus.  

### <a name="blob-level-tiering-billing"></a>Facturation de la hiérarchisation au niveau de l’objet blob

Quand un objet blob est déplacé vers un niveau plus froid (chaud -> froid, chaud -> archive ou froid -> archive), l’opération est facturée comme une opération d’écriture dans le niveau de destination, où les taux du niveau de destination s’appliquent également aux opérations d’écriture (par 10 000) ainsi qu’à l’écriture des données (par Go).

Lorsqu’un objet blob est déplacé vers un niveau plus chaud (archive -> froid, archive -> chaud ou froid -> chaud), l’opération est facturée comme une lecture à partir du niveau source, où l’opération de lecture (par 10 000) et les frais d’extraction (par Go) de données du niveau source s’appliquent. Le tableau suivant résume la facturation des changements de niveau.

| | **Tarif d'écriture (opération + accès)** | **Tarif de lecture (opération + accès)**
| ---- | ----- | ----- |
| **Direction SetBlobTier** | hot->cool, hot->archive, cool->archive | archive->cool, archive->hot, cool->hot

Si vous passez le niveau du compte de chaud à froid, vous sera facturé pour les opérations d’écriture (par 10 000) pour tous les objets blobs ne disposant pas d’un niveau configuré dans les comptes GPv2 uniquement. Ce changement n’est pas facturé dans les comptes de stockage Blob. Vous serez facturé pour les opérations d’écriture (par 10 000) et l’extraction de données (par Go) si vous passez votre compte de stockage d’objets blob ou GPv2 de froid à chaud. Des frais de suppression anticipée peut également s’appliquer pour tout objet blob déplacé hors du niveau froid ou archive.

### <a name="cool-and-archive-early-deletion"></a>Suppression anticipée dans les niveaux Froid et Archive

En plus des frais par Go et par mois, chaque objet blob déplacé dans le niveau froid (comptes GPv2 uniquement) est sujet à une période de suppression anticipée froide de 30 jours, et chaque objet blob déplacé dans le niveau archive est sujet à une période de suppression anticipée d’archive de 180 jours. Ces charges sont calculées au prorata. Par exemple, si un objet blob est déplacé en archive puis supprimé ou déplacé vers le niveau chaud après 45 jours, il vous sera facturé des frais de suppression anticipé équivalent à 135 (180 moins 45) jours de stockage de cet objet blob en archive.

Vous pouvez calculer la suppression anticipée en utilisant la propriété blob, **creation-time** s’il n’y a pas eu de changement de niveau d’accès. Sinon, vous pouvez utiliser lorsque le niveau d’accès a été modifié à froid ou archive en consultant la propriété d’objet blob : **access-tier-modification-time**. Pour plus d’informations sur les propriétés de l’objet blob, consultez [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparaison des options de stockage blob de bloc

Le tableau présente une comparaison du stockage blob par blocs performances premium et chaud, froid et archive suivant niveaux d’accès.

|                                           | **Performances de niveau Premium** | **Niveau chaud** | **Niveau froid** | **Niveau d’archive**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Disponibilité**                          | 99,9 %            | 99,9 %        | 99 % | S.O. |
| **Disponibilité** <br> **(Lectures RA-GRS)**  | S.O.              | 99,99 %       | 99,9 % | S.O. |
| **Frais d’utilisation**                         | Coûts de stockage supérieurs, accès inférieur et le coût de transaction | Coûts de stockage supérieurs, coûts d'accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d'accès et de transaction les plus élevés |
| **Taille minimale des objets**                   | S.O. | N/A | N/A | S.O. |
| **Durée de stockage minimale**              | S.O. | S.O. | 30 jours (GPv2 uniquement) | 180 jours
| **Latence** <br> **(Temps jusqu’au premier octet)** | Millisecondes de chiffre | millisecondes | millisecondes | < 15 h

> [!NOTE]
> Pour les cibles d’évolutivité et les performances, consultez [objectifs de performance et d’extensibilité Azure Storage pour les comptes de stockage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Scénarios de démarrage rapide

Dans cette section, les scénarios suivants sont décrits à l’aide du Portail Azure :

- Comment changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob.
- Comment changer le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets blob.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’accès de compte par défaut d’un compte de stockage GPv2 ou d’objets blob

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Pour accéder à votre compte de stockage, sélectionnez Toutes les ressources, puis sélectionnez votre compte de stockage.

1. Dans le panneau Paramètres, cliquez sur **Configuration** pour afficher et/ou modifier la configuration du compte.

1. Sélectionnez le niveau d’accès approprié à vos besoins : Affectez la valeur **Froid** ou **Chaud** au **Niveau d’accès**.

1. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modifier le niveau d’un objet blob dans un compte de stockage GPv2 ou d’objets Blob

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Pour accéder à votre objet blob dans votre compte de stockage, sélectionnez **toutes les ressources**, sélectionnez votre compte de stockage, sélectionnez votre conteneur, puis sélectionnez votre objet blob.

1. Dans le **propriétés d’objets Blob** panneau, sélectionnez le **niveau d’accès** menu déroulant pour sélectionner le **chaud**, **froid**, ou **Archive**  niveau d’accès.

1. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

## <a name="pricing-and-billing"></a>Tarification et facturation

Tous les comptes de stockage utilisent un modèle tarifaire pour le stockage Blob basé sur le niveau de chaque objet blob. Gardez à l’esprit les conditions de facturation suivantes :

- **Coûts de stockage** : En plus de la quantité de données stockées, le coût de stockage des données varie selon le niveau d’accès. Le coût par gigaoctet diminue à mesure que le niveau refroidit.
- **Coûts d’accès aux données** : les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données dans le niveau d’accès froid et archive, les frais d’accès par gigaoctet données pour les lectures sont facturés.
- **Coûts de transaction** : il existe des frais par transaction pour tous les niveaux, augmentant à mesure que le niveau refroidit.
- **Coûts de transfert de données de géoréplication** : ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, notamment GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.
- **Coûts de transfert de données sortantes** : les transferts de données sortantes (données transférées hors d’une région Azure) sont facturés pour l’utilisation de la bande passante par gigaoctet. Cette facturation est cohérente avec les comptes de stockage à usage général.
- **La modification du niveau d’accès**: Modifier le niveau d’accès de compte du niveau froid au niveau chaud entraîne des frais correspondant à la lecture de toutes les données existantes dans le compte de stockage. Par ailleurs, le passage du niveau d’accès de stockage chaud au niveau d’accès de stockage froid implique des frais correspondant à l’écriture de toutes les données dans le niveau froid (comptes GPv2 uniquement).

> [!NOTE]
> Pour plus d’informations sur les tarifs des comptes de stockage Blob, consultez la page [Présentation de la tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Forum Aux Questions

**Dois-je utiliser un compte de stockage GPv2 ou d’objets blob si je souhaite niveler mes données ?**

Nous vous recommandons d’utiliser des comptes GPv2 pour la hiérarchisation à la place des comptes de stockage d’objets blob. GPv2 prend en charge toutes les fonctionnalités prises en charge par les comptes de stockage d’objets blob, plus beaucoup d’autres. La tarification entre le stockage d’objets blob et GPv2 est presque identique, mais certaines nouvelles fonctionnalités et réduction de tarifs sont uniquement disponibles sur les comptes GPv2. Les comptes GPv1 ne prennent pas en charge la hiérarchisation.

La structure de tarification entre des comptes GPv1 et GPv2 est différente et les clients doivent évaluer soigneusement le fait de passer à des comptes GPv2. Vous pouvez facilement convertir un compte de stockage d’objets blob ou GPv1 existant vers un compte GPv2 en un simple clic. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

**Puis-je stocker des objets dans les trois (chaud, froid et archive) niveaux dans le même compte d’accès ?**

Oui. L’attribut **Niveau d’accès** configuré au niveau du compte est le niveau par défaut applicable à tous les objets du compte ne possédant par un niveau d’ensemble explicite. Toutefois, la hiérarchisation au niveau de l’objet blob permet de définir le niveau d’accès au niveau de l’objet, quel que soit le paramètre de niveau accès du compte. Objets BLOB dans un des niveaux d’accès de trois (chaud, froid ou archive) peuvent exister dans le même compte.

**Puis-je modifier le niveau d’accès par défaut de mon compte de stockage Blob ou GPv2 ?**

Oui, vous pouvez modifier le niveau d’accès par défaut en définissant le **niveau d’accès** attribut sur le compte de stockage. La modification du niveau d’accès s’applique à tous les objets stockés dans le compte qui n’ont pas de définir un niveau explicite. Activation/la désactivation du niveau d’accès à partir de chaud à froid implique des opérations d’écriture (par 10 000) pour tous les objets BLOB sans niveau ensemble dans des comptes GPv2 uniquement et activation/désactivation de froid à chaud implique les deux opérations de lecture (par 10 000) et des frais de récupération de données (par Go) pour tous les objets BLOB dans stockage Blob et les comptes GPv2.

**Puis-je configurer le niveau d’accès par défaut de mon compte sur Archive ?**

Non. Uniquement les niveaux d’accès chaud et froid peuvent être définies en tant que le niveau d’accès de compte par défaut. Le niveau archive ne peut être choisi qu’au niveau d’un objet.

**Dans lequel les régions sont la chaud, froid et archiver des niveaux d’accès disponibles dans ?**

Les niveaux d’accès chaud et froid, ainsi que la hiérarchisation au niveau de l’objet blob sont disponibles dans toutes les régions. Le stockage archive ne sera disponible au départ que dans certaines régions sélectionnées. Pour avoir une liste complète, voir [Régions Azure disponibles par région](https://azure.microsoft.com/regions/services/).

**Les objets BLOB dans le niveau d’accès froid se comportent différemment de ceux dans le niveau d’accès chaud ?**

Les objets BLOB dans le niveau d’accès chaud ont la même latence que les objets BLOB des comptes de stockage Blob, GPv1 et GPv2. Les objets BLOB dans le niveau d’accès froid ont une latence similaire (en millisecondes) en tant qu’objets BLOB des comptes de stockage Blob, GPv1 et GPv2. Les objets BLOB dans le niveau d’accès archive ont plusieurs heures de latence dans les comptes de stockage Blob, GPv1 et GPv2.

Les objets BLOB dans le niveau d’accès froid ont un niveau service disponibilité de légèrement inférieur (SLA) que les objets BLOB stockés au niveau de l’accès à chaud. Pour plus d’informations, consultez la page [Contrat SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Est-ce que les opérations dans les niveaux chaud, froid et archive sont les mêmes ?**

Oui. Toutes les opérations entre les niveaux chaud et froid sont 100% cohérents. Toutes les opérations d’archive valides, comprenant la suppression, la liste, l’obtention des propriétés/médadonnées d’objets blob, et le niveau de l’objet blob sont 100 % cohérents entre les niveaux chaud et froid. Un objet blob ne peut pas être lu ou modifié en étant dans le niveau archive.

**Lors de la réalimentation d’un objet blob depuis le niveau archive vers le niveau chaud ou froid, comment serais-je averti de la fin de la réalimentation ?**

Lors de la réalimentation, vous pouvez utiliser l’opération d’obtention des propriétés d’objet blob afin d’interroger l’attribut **État d’archive** pour confirmer la fin du changement de niveau. L’état affiche « réalimentation-vers-chaud » ou « réalimentation-vers-froid » selon le niveau choisi. Une fois le processus terminé, la propriété d’état archive est supprimée, et la propriété **Niveau d’accès** de l’objet blob indique le niveau chaud ou froid.  

**Après le réglage du niveau d’un objet blob, quand vais-je commencer à être facturé au taux approprié ?**

Chaque objet blob est toujours facturé en fonction du niveau indiqué par la propriété **Niveau d’accès** de l’objet blob. Quand vous changez le niveau d’un objet blob, la propriété **Niveau d’accès** reflète instantanément le nouveau niveau pour toutes les transitions. Toutefois, la réactivation d’un objet blob d’un niveau archive vers un niveau chaud ou froid peut prendre plusieurs heures. Dans ce cas, vous êtes facturé aux tarifs archive jusqu’à ce que la réactivation soit terminée et que la propriété **Niveau d’accès** reflète le nouveau niveau. À partir de là, l’objet blob vous est facturé au tarif chaud ou froid.

**Comment puis-je déterminer si je vais faire l’objet de frais de suppression anticipée lors de la suppression ou du déplacement d’un objet blob en dehors du niveau froid ou archive ?**

Tout objet blob supprimé ou déplacé en dehors du niveau froid (comptes GPv2 uniquement) ou archive respectivement avant 30 jours et 180 jours fera l’objet de frais de suppression anticipée calculés au prorata. Vous pouvez déterminer combien de temps un objet blob est resté au niveau froid ou archive en vérifiant sa propriété **Access Tier Change Time** (Heure du changement de niveau d’accès), qui horodate le dernier changement de niveau. Pour plus d’informations, consultez [suppression anticipée froid et archive](#cool-and-archive-early-deletion).

**Quels outils et kits de développement Azure prennent en charge la hiérarchisation au niveau de l’objet blob et le stockage archive ?**

Le portail Azure, PowerShell et les outils CLI et les bibliothèques de client .NET, Java, Python, et Node.js prennent toutes en charge la hiérarchisation au niveau de l’objet blob et le stockage archive.  

**Combien de données puis-je stocker dans les niveaux chaud, froid et archive ?**

Stockage de données, ainsi que d’autres limites sont définies au niveau du compte et non par le niveau d’accès. Par conséquent, vous pouvez choisir d’utiliser toute votre limite sur un seul niveau ou sur les trois niveaux. Pour plus d’informations, consultez [Objectifs d’extensibilité et de performances de stockage Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Évaluer les niveaux chaud, froid et archive dans les comptes de stockage d’objets blob GPv2

[Vérifier la disponibilité de niveau chaud, froid et archive par région](https://azure.microsoft.com/regions/#services)

[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)

[Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../common/storage-enable-and-view-metrics.md)

[Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)

---
title: (DÉPRÉCIÉ) Superviser un cluster DC/OS Azure – Pile ELK
description: Surveillez un cluster DC/OS dans un cluster Azure Container Service avec ELK (Elasticsearch, Logstash et Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998139"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(DÉPRÉCIÉ) Superviser un cluster Azure Container Service avec ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dans cet article, nous montrons comment déployer la pile ELK (Elasticsearch, Logstash, Kibana) sur un cluster DC/OS dans Azure Container Service. 

## <a name="prerequisites"></a>Prérequis
[Déployez](container-service-deployment.md) et [connectez](../container-service-connect.md) un cluster DC/OS configuré par Azure Container Service. Explorez le tableau de bord DC/OS et les services Marathon [ici](container-service-mesos-marathon-ui.md). Installez également [l’équilibrage de charge Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
La pile ELK est une combinaison d’Elasticsearch, de Logstash et de Kibana qui fournit une pile de bout en bout pouvant être utilisée pour surveiller et analyser les journaux d’activité de votre cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurer la pile ELK sur un cluster DC/OS
Accéder à votre interface utilisateur DC/OS via [http://localhost:80/](http://localhost:80/), puis rendez-vous dans **Univers**. Recherchez et installez Elasticsearch, Logstash et Kibana dans l’univers du contrôleur de domaine/système d’exploitationDC/OS et dans cet ordre spécifique. Vous pouvez en savoir plus sur la configuration en cliquant sur le lien **Advanced Installation (Installation avancée)**.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Une fois les conteneurs opérationnels, vous devez activer Kibana pour qu’ils soient accessibles via Marathon-LB. Accédez à **Services** > **kibana**, puis cliquez sur **Modifier** comme indiqué ci-dessous.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Basculez en **mode JSON** et défilez jusqu’à la section des étiquettes.
Vous devez ajouter une entrée `"HAPROXY_GROUP": "external"` ici comme indiqué ci-dessous.
Une fois que vous cliquez sur **Deploy changes** (Déployer les changements), votre conteneur redémarre.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Si vous souhaitez vérifier que Kibana est enregistré en tant que service dans le tableau de bord HAPROXY, vous devez ouvrir le port 9090 sur le cluster de l’agent, car HAPROXY s’exécute sur le port 9090.
Par défaut, nous ouvrons les ports 80, 8080 et 443 dans le cluster de l’agent DC/OS.
Vous trouverez les instructions pour ouvrir un port et fournir une évaluation publique [ici](container-service-enable-public-access.md).

Pour accéder au tableau de bord HAPROXY, ouvrez l’interface d’administration Marathon-LB à l’adresse : `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Une fois que vous accédez à l’URL, le tableau de bord HAPROXY s’affiche comme indiqué ci-dessous, et vous devriez voir une entrée de service pour Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Pour accéder au tableau de bord Kibana, qui est déployé sur le port 5601, vous devez ouvrir le port 5601. Suivez les instructions [ici](container-service-enable-public-access.md). Ouvrez ensuite le tableau de bord Kibana à l’adresse : `http://localhost:5601`.

## <a name="next-steps"></a>Étapes suivantes

* Pour le transfert et la configuration du journal système et d’application, consultez [Log Management in DC/OS with ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/) (Gestion de journaux dans DC/OS avec ELK).

* Pour filtrer les journaux d’activité, consultez [Filtering Logs with ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/) (Filtrage des journaux d’activité avec ELK). 

 


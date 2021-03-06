---
title: Mappage de schéma dans l’activité de copie | Microsoft Docs
description: Découvrez comment l’activité de copie dans Azure Data Factory mappe les schémas et les types de données des données de la source aux données du récepteur lors de la copie.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267121"
---
# <a name="schema-mapping-in-copy-activity"></a>Mappage de schéma dans l’activité de copie
Cet article décrit la manière dont l’activité de copie d’Azure Data Factory effectue un mappage de schéma et de type de données, des données sources au données du récepteur lors de la copie.

## <a name="column-mapping"></a>Mappage de colonnes

Le mappage de colonnes s’applique lors de la copie des données entre des données au format tabulaire. Par défaut, l’activité de copie **mappe les données sources au récepteur par noms de colonnes**, sauf si un [mappage de colonnes explicite](#explicit-column-mapping) est configuré. Plus spécifiquement, l’activité de copie comprend les trois phases suivantes :

1. Lire les données de la source et déterminer le schéma de celle-ci

    * Pour les sources de données ayant un schéma prédéfini dans la banque de données/format de fichier, par exemple, les bases de données/fichiers contenant des métadonnées (Avro/ORC/Parquet/Texte avec en-tête), le schéma de la source est extrait du résultat de la requête ou des métadonnées du fichier.
    * Pour les sources de données dont le schéma est flexible, par exemple, Table Azure/Cosmos DB, le schéma de la source est déduit du résultat de la requête. Vous pouvez le remplacer en configurant la « structure » dans le jeu de données.
    * Pour un fichier Texte sans en-tête, les noms de colonnes par défaut sont générés avec le modèle « Prop_0 », « Prop_1 », etc. Vous pouvez le remplacer en configurant la « structure » dans le jeu de données.
    * Pour la source Dynamics, vous devez fournir les informations de schéma dans la section « structure » du jeu de données.

2. Appliquer le mappage de colonnes explicite s’il est spécifié.

3. Écrire les données sur le récepteur

    * Pour les banques de données avec un schéma prédéfini, les données sont écrites dans les colonnes du même nom.
    * Pour les banques de données sans schéma fixe et pour les formats de fichiers, les noms de colonne/métadonnées sont générés en fonction du schéma de la source.

### <a name="explicit-column-mapping"></a>Mappage de colonnes explicite

Vous pouvez spécifier **columnMappings** dans la section **typeProperties** de l’activité de copie afin d’effectuer un mappage de colonnes explicite. Dans ce scénario, la section « structure » est requise pour les jeux de données d’entrée et de sortie. Le mappage de colonnes prend en charge le **mappage de la totalité ou d’un sous-ensemble des colonnes de la « structure » du jeu de données de la source à toutes les colonnes de la « structure » du jeu de données du récepteur**. Voici une liste de conditions d’erreur qui entraînent la levée d’une exception :

* Le résultat de la requête de banque de données source n’a pas de nom de colonne spécifié dans la section « structure » du jeu de données d’entrée.
* La banque de données du récepteur (si un schéma est prédéfini) n’a pas de nom de colonne spécifié dans la section « structure » du jeu de données de sortie.
* La « structure » du jeu de données du récepteur contient un nombre de colonnes inférieur ou supérieur à celui spécifié par le mappage.
* Mappage en double.

#### <a name="explicit-column-mapping-example"></a>Exemple de mappage de colonnes explicite

Dans cet exemple, la table d’entrée possède une structure et pointe vers une table dans une base de données SQL locale.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Dans cet exemple, la table de sortie possède une structure et pointe vers une table dans une base de données Azure SQL Database.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Le JSON suivant définit une activité de copie dans un pipeline. Les colonnes de la source sont mappées aux colonnes dans le récepteur (**columnMappings**) à l’aide de la propriété **Translator**.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Si vous utilisiez la syntaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` pour spécifier le mappage de colonnes, il est toujours pris en charge tel quel.

**Flux du mappage de colonnes :**

![Flux du mappage de colonnes](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Mappage de schéma

Le mappage de schéma s'applique lors de la copie de données entre des données au format hiérarchique et tabulaire, par exemple la copie à partir de MongoDB/REST vers un fichier texte et la copie à partir de SQL vers l'API Azure Cosmos DB pour MongoDB. Les propriétés suivantes sont prises en charge dans la section `translator` de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du traducteur d’activité de copie doit être définie sur : **TabularTranslator** | Oui |
| schemaMapping | Une collection de paires clé-valeur, qui représente la relation de mappage **du côté source au récepteur côté**.<br/>- **Clé :** représente source. Pour **sources tabulaires**, spécifiez le nom de colonne tel que défini dans la structure du jeu de données ; pour **source hiérarchique**, spécifiez l’expression de chemin JSON pour chaque champ à extraire et mapper.<br/>- **Valeur :** représente récepteur. Pour **récepteur tabulaire**, spécifiez le nom de colonne tel que défini dans la structure du jeu de données ; pour **récepteur hiérarchique**, spécifiez l’expression de chemin JSON pour chaque champ à extraire et à mapper. <br/> Dans le cas des données hiérarchiques, pour les champs sous l’objet racine, chemin d’accès JSON commence par $ racine ; pour les champs dans le tableau sélectionné par `collectionReference` propriété, chemin d’accès JSON commence à partir de l’élément de tableau.  | Oui |
| collectionReference | Si vous souhaitez effectuer une itération et extraire des données à partir des objets situés **à l’intérieur d’un champ de tableau** présentant le même modèle et effectuer une conversion par ligne et par objet, spécifiez le chemin JSON de ce tableau afin d’effectuer une application croisée. Cette propriété est prise en charge uniquement quand des données hiérarchiques sont la source. | Non  |

**Exemple : copier à partir de MongoDB vers SQL :**

Par exemple, si vous avez un document MongoDB avec le contenu suivant : 

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

Vous souhaitez copier ce fichier dans une table SQL Azure au format suivant, en mettant à plat les données se trouvant dans le tableau *(order_pd and order_price)* et en effectuant une jointure croisée avec les informations racines communes *(numéro, date et ville)*  :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configurez la règle de mappage de schéma comme l’exemple JSON d’activité de copie suivant :

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mappage de type de données

L’activité de copie effectue un mappage des types de la source aux types du récepteur selon l’approche en 2 étapes suivante :

1. Conversion des types de données natifs de la source en types de données intermédiaires d’Azure Data Factory
2. Conversion des types de données intermédiaires d’Azure Data Factory en types de données natifs du récepteur

Vous pouvez trouver le mappage du type natif au type intermédiaire dans la section « Mappage de type de données » de chaque rubrique du connecteur.

### <a name="supported-data-types"></a>Types de données pris en charge

Data Factory prend en charge les types de données intermédiaires suivants : Vous pouvez spécifier les valeurs ci-dessous lors de la configuration des informations de type dans la configuration de [structure du jeu de données](concepts-datasets-linked-services.md#dataset-structure) :

* Byte[]
* Booléen
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* Chaîne
* Timespan

### <a name="explicit-data-type-conversion"></a>Conversion de type de données explicite

Lors de la copie de données vers des banques de données au schéma fixe, par exemple, SQL Server/Oracle, quand la source et le récepteur on un type différent sur la même colonne, la conversion de type explicite doit être déclarée côté source :

* Pour un source fichier, par exemple CSV/Avro, la conversion de type doit être déclarée via la structure de la source avec la liste complète des colonnes (nom de colonne côté source et type côté récepteur).
* Pour une source relationnelle (par exemple, SQL/Oracle), la conversion de type doit être effectuée à l’aide un cast de type explicite dans l’instruction de requête.

## <a name="when-to-specify-dataset-structure"></a>Quand spécifier la « structure » du jeu de données

Une « structure » est requise pour le jeu de données dans les scénarios ci-dessous :

* Application d’une [conversion de type de données explicite](#explicit-data-type-conversion) pour les sources de fichier pendant la copie (jeu de données d’entrée).
* Application d’un [mappage de colonnes explicite](#explicit-column-mapping) pendant la copie (jeu de données tant d’entrée que de sortie).
* Copie à partir d’une source Dynamics 365/CRM (jeu de données d’entrée).
* Copie vers Cosmos DB en tant qu’objet imbriqué quand la source n’est pas constituée de fichiers JSON (jeu de données de sortie).

Une « structure » est suggérée pour le jeu de données dans les scénarios ci-dessous :

* Copie à partir d’un fichier Texte sans en-tête (jeu de données d’entrée). Vous pouvez spécifier les noms de colonnes pour un fichier Texte, qui s’alignent sur les colonnes correspondantes dans le récepteur, pour éviter de devoir configurer un mappage de colonnes explicite.
* Copie à partir de banques de données au schéma flexible, par exemple, Azure Table/Cosmos DB (jeu de données d’entrée), pour garantir que les données attendues (colonnes) sont copiées au lieu de laisser l’activité de copie déduire le schéma sur la base des lignes supérieures lors de l’exécution de chaque activité.


## <a name="next-steps"></a>Étapes suivantes
Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md)
- [Performances de l’activité de copie](copy-activity-performance.md)

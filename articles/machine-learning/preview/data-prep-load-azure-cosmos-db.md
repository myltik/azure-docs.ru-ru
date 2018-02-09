---
title: "Подключение Azure Cosmos DB в качестве источника данных в Azure Machine Learning Workbench | Документация Майкрософт"
description: "В этом документе содержатся примеры подключения к Azure Cosmos DB через Azure Machine Learning Workbench."
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 22e4aedef1c00add17a9c3c4d0ed3822de155a6d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Подключение к Azure Cosmos DB в качестве источника данных
В этой статье содержатся примеры Python, позволяющие подключаться к Cosmos DB в Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Загрузка данных Azure Cosmos DB в средство подготовки данных

Создайте поток данных на основе скрипта и загрузите данные из Azure Cosmos DB с помощью следующего скрипта: 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Другие соединения с источниками данных
Другие примеры дополнительных подключений к исходным данным см. [здесь](data-prep-appendix8-sample-source-connections-python.md).

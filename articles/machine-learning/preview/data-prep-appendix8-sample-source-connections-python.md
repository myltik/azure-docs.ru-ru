---
title: "Примеры дополнительных подключений к исходным данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры подключений к исходным данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 9aaf4329b25cb189146949afed89cf15619ba592
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Пример пользовательских подключений к источнику (Python) 
Перед ознакомлением с этим приложением см. [общие сведения о расширяемости Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Загрузка данных с сайта data.world

### <a name="prerequisites"></a>Предварительные требования

#### <a name="register-yourself-at-dataworld"></a>Регистрация на сайте data.world
Необходимо получить токен API с веб-сайта data.world.

#### <a name="install-dataworld-library"></a>Установка библиотеки data.world

Откройте интерфейс командной строки Azure Machine Learning Workbench, выбрав **Файл** > **Открыть интерфейс командной строки**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

В окне командной строки выполните команду `dw configure`. Отобразится запрос на предоставление токена. После ввода токена в корневом каталоге создается каталог .dw/ directory, где он и хранится.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Теперь вы можете импортировать библиотеки data.world.

#### <a name="load-data-into-data-preparation"></a>Загрузка данных в средство подготовки

Создайте поток данных на основе скрипта. Затем выполните следующий скрипт, чтобы загрузить данные из data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Загрузка данных Azure Cosmos DB в средство подготовки данных

Создайте поток данных на основе скрипта и загрузите данные из Azure Cosmos DB с помощью следующего скрипта: (Эти библиотеки необходимо сначала установить. Дополнительные сведения см. в предыдущем документе, на который мы указали ссылку.)

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

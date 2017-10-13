---
title: "Примеры дополнительных подключений к данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure | Документация Майкрософт"
description: "В этой статье приведены примеры подключений к исходным данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure."
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
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Пример пользовательских подключений к источнику (Python) 
Перед ознакомлением с этим приложением прочтите статью [Расширения Python для подготовки данных](data-prep-python-extensibility-overview.md).

## <a name="loading-data-from-dataworld"></a>Загрузка данных с сайта data.world

### <a name="prerequisites"></a>Предварительные требования

#### <a name="register-yourself-at-dataworld"></a>Регистрация на сайте data.world
Чтобы загрузить данные с сайта data.world, требуется токен API.

#### <a name="install-dataworld-library"></a>Установка библиотеки data.world

Откройте интерфейс командной строки Azure Machine Learning Workbench, выбрав _Файл->Open command-line interface_ (Открыть интерфейс командной строки).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

В окне командной строки выполните команду `dw configure`, после чего отобразится запрос на предоставление токена. После ввода токена в корневом каталоге создается каталог .dw/ directory, где он и хранится.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Теперь вы можете импортировать библиотеки data.world.

#### <a name="load-data-into-data-preparation"></a>Загрузка данных в средство подготовки

Создайте поток данных на основе скрипта и загрузите данные из data.world с помощью следующего скрипта:

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>Загрузка данных CosmosDB в средство подготовки

Создайте поток данных на основе скрипта и загрузите данные из базы данных CosmosDB с помощью скрипта ниже. Библиотеки сначала нужно установить. Дополнительные сведения см. в справочном документе, ссылка на который предоставлена выше.

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

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

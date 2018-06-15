---
title: Примеры дополнительных подключений к исходным данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure | Документация Майкрософт
description: В этой статье приведены примеры подключений к исходным данным, поддерживаемых в средстве подготовки данных службы машинного обучения Azure
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: b87d88a5bb7846894c425e701a073707ddd1f3be
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831192"
---
# <a name="sample-of-custom-source-connections-python"></a>Пример пользовательских подключений к источнику (Python) 
Перед ознакомлением с этим приложением см. [общие сведения о расширяемости Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Загрузка данных с сайта data.world

### <a name="prerequisites"></a>предварительным требованиям

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

Создайте преобразование "Преобразование потока данных (скрипт)". Затем выполните следующий скрипт, чтобы загрузить данные из data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Использование Azure Cosmos DB в качестве точки подключения к источнику данных
Пример использования Azure Cosmos DB в качестве точки подключения к источнику данных см. в статье [Подключения к Azure Cosmos DB в качестве источника данных](data-prep-load-azure-cosmos-db.md).

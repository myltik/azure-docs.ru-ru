---
title: Подробное руководство по API выполнения средств подготовки данных в службе "Машинное обучения Azure" | Документация Майкрософт
description: В этой статье содержатся сведения о выполнении ранее созданных пакетов источников данных и подготовки данных.
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
ms.openlocfilehash: dac13d36477dccce3bce2ecb9fe2326c36d9ef59
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830601"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Выполнение пакетов источников данных и подготовки данных с помощью Python

Чтобы использовать пакеты источников данных и подготовки данных с Python, сделайте следующее:

1. Перейдите на вкладку **Данные** проекта.

2. Щелкните соответствующий источник правой кнопкой мыши.

3. Выберите **Создать файл кода доступа к данным**.

Это действие создает короткий скрипт Python, который выполняет пакет и возвращает блок данных.

## <a name="data-sources"></a>Источники данных

В модуле `azureml.dataprep.datasource` реализована одна функция для выполнения источника данных и возвращения блока данных `load_datasource(path, secrets=None, spark=None)`.
- `path` — путь к источнику данных (файл DSOURCE).
- `secrets` — необязательный словарь, сопоставляющий ключи с секретами.
- `spark` — необязательное логическое значение, указывающее, следует ли возвращать блок данных Spark или Pandas. По умолчанию Azure Machine Learning Workbench определяет возвращаемый в среде выполнения блок данных на основе контекста.

## <a name="data-preparations-packages"></a>Пакеты подготовки данных

Модуль `azureml.dataprep.package` содержит три функции, выполняющие поток данных из пакета подготовки данных.

### <a name="execution-functions"></a>Функции выполнения

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` — отправляет указанный поток данных для выполнения, но не возвращает блок данных.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` — выполняет указанный поток данных и возвращает результаты в виде блока данных.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)`— выполняет указанный поток данных на основе выполняющегося в памяти источника данных и возвращает результаты в виде блока данных. Аргумент `user_config` — это словарь, сопоставляющий полный путь источника данных (файл DSOURCE) с выполняющимся в памяти источником данных, представленным в виде списка списков.

### <a name="common-arguments"></a>Общие аргументы

- `package_path` — путь к пакету подготовки данных (файл DPREP).
- `dataflow_idx` — начинающийся с нуля индекс потока данных в пакете для выполнения. Если указанный поток данных ссылается на другие потоки или источники данных, они также выполняются.
- `secrets` — необязательный словарь, сопоставляющий ключи с секретами.
- `spark` — необязательное логическое значение, указывающее, следует ли возвращать блок данных Spark или Pandas. По умолчанию Workbench определяет возвращаемый в среде выполнения блок данных на основе контекста.

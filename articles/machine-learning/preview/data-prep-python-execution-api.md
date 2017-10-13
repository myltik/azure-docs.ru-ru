---
title: "Комплексное руководство по API выполнения средства подготовки данных в службе машинного обучения Azure | Документация Майкрософт"
description: "В этой статье содержатся сведения о выполнении ранее созданных пакетов источников данных и подготовки данных."
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
ms.date: 09/12/2017
ms.openlocfilehash: ceecc718f8198e9a88cae4acbe97e7f26d95f984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="executing-data-sources-and-data-preparation-packages-from-python"></a>Выполнение пакетов источников данных и подготовки данных в Python

Чтобы использовать пакет источников данных и подготовки данных в Python, сделайте следующее:
- Перейдите на вкладку "Данные" проекта.
- Щелкните соответствующий источник правой кнопкой мыши.
- Выберите Generate Data Access Code File (Создать файл кода доступа к данным).

Это действие создает короткий скрипт Python, который выполняет пакет и возвращает блок данных.

## <a name="data-sources"></a>Источники данных

В модуле `azureml.dataprep.datasource` реализована одна функция для выполнения источника данных и возвращения блока данных `load_datasource(path, secrets=None, spark=None)`.
- `path` — путь к источнику данных (файл DSOURCE).
- `secrets` — необязательный словарь, сопоставляющий ключи с секретами.
- `spark` — необязательное логическое значение, указывающее, следует ли возвращать блок данных Spark или Pandas. По умолчанию Azure ML Workbench определяет возвращаемый в среде выполнения блок данных на основе контекста.

## <a name="data-preparation-packages"></a>Пакеты подготовки данных

Модуль `azureml.dataprep.package` содержит три функции, выполняющие поток данных из пакета подготовки данных.

### <a name="execution-functions"></a>Функции выполнения

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` — отправляет указанный поток данных для выполнения, но не возвращает блок данных.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` — выполняет указанный поток данных и возвращает результаты в виде блока данных.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` — выполняет указанный поток данных на основе выполняющегося в памяти источника данных и возвращает результаты в виде блока данных. Аргумент `user_config` — это словарь, сопоставляющий абсолютный путь источника данных (файл DSOURCE) с выполняющимся в памяти источником данных, представленным в виде списка списков.

### <a name="common-arguments"></a>Общие аргументы

- `package_path` — путь к пакету подготовки данных (файл DPREP).
- `dataflow_idx` — начинающийся с нуля индекс потока данных в пакете для выполнения. Если указанный поток данных ссылается на другие потоки или источники данных, они также выполняются.
- `secrets` — необязательный словарь, сопоставляющий ключи с секретами.
- `spark` — необязательное логическое значение, указывающее, следует ли возвращать блок данных Spark или Pandas. По умолчанию Azure ML Workbench определяет возвращаемый в среде выполнения блок данных на основе контекста.

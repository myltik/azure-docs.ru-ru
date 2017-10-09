---
title: "Как использовать функцию \"Сбор данных\" службы \"Управление моделями\" в Azure Machine Learning Workbench | Документация Майкрософт"
description: "В этом документе рассказывается об использовании функции \"Сбор данных\" службы \"Управление моделями\" в Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-collect-model-data-using-data-collection"></a>Как собирать данные модели с помощью функции сбора данных

Функция сбора данных модели позволяет архивировать входные данные и прогнозы модели из веб-службы машинного обучения. В этом документе рассказывается о следующих аспектах сбора данных модели:

- Установка пакета сбора данных.
- Использование сбора данных.
- Просмотр и использование собранных данных.

## <a name="how-to-install-the-data-collection-package"></a>Установка пакета сбора данных
Библиотеку сбора данных можно изначально установить на компьютере с Linux и Windows.

### <a name="windows"></a>Windows
На компьютере с Windows модуль сборщика данных можно установить с помощью следующей команды:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
На компьютере с Linux сначала необходимо установить библиотеку libxml ++. Выполните следующую команду, используя sudo:

    sudo apt-get install libxml++2.6-2v5

Затем выполните следующую команду:

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>Использование сбора данных

Чтобы использовать модель сбора данных, необходимо внести следующие изменения в файл оценки:

1. Добавьте следующий код в начало файла:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Добавьте в функцию `init()` следующие строки кода:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Добавьте в функцию `run(input_df)` следующие строки кода:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Убедитесь, что переменные `input_df` и `pred` (прогнозируемое значение `model.predict()`) инициализированы перед вызовом функции `collect()` для них.

4. Используйте команду `az ml service create realtime` с параметром `--collect-model-data true` для создания веб-службы, работающей в реальном времени. Таким образом данные модели будут собираться при запуске службы.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Чтобы проверить, собираются ли данные, выполните команду `az ml service run realtime`.

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>Просмотр и использование собранных данных
Чтобы просмотреть собранные данные в хранилище больших двоичных объектов, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Другие службы**.
3. В поле поиска введите `Storage accounts` и нажмите клавишу **ВВОД**.
4. В колонке поиска **Учетные записи хранения** выберите ресурс **Учетная запись хранения**. Чтобы определить учетную запись хранения, сделайте следующее:

    а. Перейдите к Azure ML Workbench, выберите рабочий проект и откройте командную строку из меню **Файл**.
    
    b. Введите `az ml env show -v` и проверьте значение *storage_account*. Это и есть имя учетной записи хранения.

5. В меню колонки ресурсов щелкните **Контейнеры**, а затем выберите контейнер **modeldata**. После первого запроса к веб-службе может пройти до 10 минут, прежде чем начнется передача данных в учетную запись хранения, поэтому нужно немного подождать. Потоки данных передаются в большие двоичные объекты по следующему пути контейнера:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Данные можно извлечь из больших двоичных объектов Azure множеством разных способов с применением программного обеспечения корпорации Майкрософт или средств с открытым кодом. Ниже приведены некоторые примеры:
 - Azure ML Workbench. Откройте CSV-файл в Azure ML Workbench, добавив его в качестве источника данных.
 - Excel. Откройте CSV-файл с данными за день в качестве электронной таблицы.
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/). Создайте диаграмму на основе данных, извлеченных из CSV-файлов больших двоичных объектов.
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview). Создайте кадр данных с крупным сегментом данных в формате .csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started). Загрузите данные в формате .csv в таблицу hive и выполняйте SQL-запросы прямо к большому двоичному объекту.



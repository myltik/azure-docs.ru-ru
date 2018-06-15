---
title: Использование функции сбора данных модели в Azure Machine Learning Workbench | Документация Майкрософт
description: В этом документе рассказывается о том, как использовать функцию сбора данных в Azure Machine Learning Workbench.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 7a76322d70f6b54d65a4b751a7187425cb4be821
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834548"
---
# <a name="collect-model-data-by-using-data-collection"></a>Сбор данных модели с помощью функции сбора данных

Функция сбора данных модели в службе "Машинное обучение Azure" позволяет архивировать входные данные и прогнозы модели из веб-службы.

## <a name="install-the-data-collection-package"></a>Установка пакета сбора данных
Библиотеку сбора данных можно изначально установить на компьютере с Linux и Windows.

### <a name="windows"></a>Windows
В Windows модуль сборщика данных можно установить с помощью следующей команды.

    pip install azureml.datacollector

### <a name="linux"></a>Linux
В Linux необходимо сначала установить библиотеку libxml++. Выполните следующую команду, используя sudo:

    sudo apt-get install libxml++2.6-2v5

Затем выполните следующую команду.

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Настройка переменных среды

Сбор данных в модели зависит от двух переменных среды. Для AML_MODEL_DC_STORAGE_ENABLED нужно установить значение **true** (прописными буквами), а для AML_MODEL_DC_STORAGE необходимо задать строку подключения для учетной записи хранения Azure, в котором вы планируете хранить данные.

Если веб-служба работает в кластере в Azure, эти переменные среды уже заданы. Если вы работаете на локальном компьютере, нужно установить их самостоятельно. Если вы используете Docker, примените параметр -e в команде docker run, чтобы передать переменные среды.

## <a name="collect-data"></a>Сбор данных

Чтобы использовать сбор данных модели, внесите следующие изменения в файл оценки.

1. Добавьте следующий код в начало файла.
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Добавьте в функцию `init()` следующие строки кода.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Добавьте в функцию `run(input_df)` следующие строки кода.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Убедитесь, что переменные `input_df` и `pred` (прогнозируемое значение `model.predict()`) инициализированы перед вызовом функции `collect()` для них.

4. Используйте команду `az ml service create realtime` с параметром `--collect-model-data true` для создания веб-службы, работающей в реальном времени. Таким образом данные модели будут собираться при выполнении службы.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Чтобы проверить, собираются ли данные, выполните команду `az ml service run realtime`.

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Просмотр собранных данных
Чтобы просмотреть собранные данные в хранилище больших двоичных объектов, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выбор пункта **Все службы**.
3. В поле поиска введите **учетные записи хранения** и нажмите клавишу ВВОД.
4. В колонке поиска **Учетные записи хранения** выберите ресурс **Учетная запись хранения**. Чтобы определить учетную запись хранения, сделайте следующее.

    a. Перейдите в Azure Machine Learning Workbench, выберите рабочий проект и откройте командную строку из меню **Файл**.
    
    Б. Введите `az ml env show -v` и проверьте значение *storage_account*. Это и есть имя учетной записи хранения.

5. В меню колонки ресурсов щелкните **Контейнеры**, а затем выберите контейнер **modeldata**. После первого запроса к веб-службе может пройти до 10 минут, прежде чем начнется передача данных в учетную запись хранения, поэтому нужно подождать. Потоки данных передаются в большие двоичные объекты по следующему пути контейнера:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Данные можно извлечь из больших двоичных объектов Azure множеством разных способов с применением программного обеспечения корпорации Майкрософт или инструментов с открытым кодом. Ниже приведены некоторые примеры:
- Azure Machine Learning Workbench. Откройте CSV-файл в Azure Machine Learning Workbench, добавив его в качестве источника данных.
- Excel. Откройте CSV-файл с данными за день в качестве электронной таблицы.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/). Создайте диаграмму на основе данных, извлеченных из CSV-файлов больших двоичных объектов.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview). Создайте кадр данных с крупным сегментом данных в формате CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started). Загрузите данные в формате CSV в таблицу Hive и выполняйте SQL-запросы прямо к большому двоичному объекту.


---
title: Создание кластера Apache Spark в Azure HDInsight | Документация Майкрософт
description: Краткое руководство HDInsight Spark по созданию кластера Apache Spark в HDInsight.
keywords: краткое руководство Spark,интерактивный запрос Spark,интерактивный запрос,Hdinsight Spark,Azure Spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 1fdae7d6e47ff70e83e8153cdc22c0b2881d2743
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Создание кластера Apache Spark в Azure HDInsight

Узнайте, как создать кластер Apache Spark в Azure HDInsight и как выполнять запросы Spark SQL к таблицам Hive. Сведения о Spark в HDInsight см. в [этой статье](apache-spark-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. См. [сведения о создании бесплатной учетной записи Azure](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Создание кластера HDInsight Spark

Создайте кластер HDInsight Spark, используя [шаблон Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). Шаблон можно найти на сайте [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Другие способы создания кластера см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Введите следующие значения.

    ![Создание кластера HDInsight Spark с использованием шаблона Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Создание кластера Spark в HDInsight с использованием шаблона Azure Resource Manager")

    * **Подписка.** Выберите подписку Azure, которая используется для создания этого кластера.
    * **Группа ресурсов.** Создайте группу ресурсов или выберите существующую. Она используется для управления ресурсами Azure для ваших проектов.
    * **Расположение**. Выберите расположение группы ресурсов. В шаблоне используется это расположение для создания кластера и его хранения по умолчанию.
    * **Имя кластера**. Введите имя создаваемого кластера HDInsight.
    * **Имя для входа в кластер и пароль**: имя для входа по умолчанию — admin. Выберите пароль для учетной записи входа в кластер.
    * **Имя пользователя SSH и пароль**. Выберите пароль пользователя SSH.

3. Установите флажок **Я принимаю указанные выше условия** и **Закрепить на панели мониторинга**, а затем нажмите кнопку **Приобрести**. Вы увидите новый элемент под названием **Развертывание для развертывания шаблона**. Процесс создания кластеров занимает около 20 минут.

Если при создании кластера HDInsight возникают проблемы, возможно, у вас нет необходимых разрешений. Дополнительные сведения см. в разделе [Требования к контролю доступа](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> В этой статье описано, как создать кластер Spark, использующий [большие двоичные объекты службы хранилища Azure в качестве хранилища кластера](../hdinsight-hadoop-use-blob-storage.md). Кроме того, вы можете создать кластер Spark, использующий [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) в качестве хранилища по умолчанию. Инструкции см. в инструкциях по [созданию кластера HDInsight с Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="create-a-jupyter-notebook"></a>Создание записной книжки Jupyter

[Записная книжка Jupyter](http://jupyter.org) — это интерактивная среда записной книжки, поддерживающая различные языки программирования, которая позволяет работать с данными, объединять код с текстом, содержащим разметку Markdown, и выполнять простую визуализацию. Spark в HDInsight также содержит [записную книжку Zeppelin](apache-spark-zeppelin-notebook.md). В этом руководстве используется записная книжка Jupyter.

**Создание записной книжки Jupyter**

1. Откройте [портал Azure](https://portal.azure.com/).

2. Откройте колонку созданного кластера Spark. Инструкции см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. На портале щелкните **Панели мониторинга кластера**, а затем — **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   ![Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook to run interactive Spark SQL query")

   > [!NOTE]
   > Для доступа к записной книжке Jupyter для кластера также можно использовать указанный ниже URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Нажмите кнопку **New** (Создать), а затем щелкните пункт **PySpark**, чтобы создать записную книжку. Записные книжки Jupyter в кластерах HDInsight поддерживают три версии ядер: **PySpark**, **PySpark3** и **Spark**. В этом руководстве используется ядро **PySpark**. Дополнительные сведения об этих ядрах и о преимуществах **PySpark** см. в статье [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

   ![Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Create a Jupyter Notebook to run interactive Spark SQL queryL")

   Будет создана и открыта записная книжка с именем Untitled (Untitled.pynb).

4. Щелкните имя записной книжки вверху и по желанию введите понятное имя.

    ![Указание имени записной книжки Jupyter, из которой будет выполняться интерактивный запрос Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Provide a name for the Jupyter notebook to run interactive Spark query from")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Выполнение инструкций Spark SQL в таблице Hive

SQL — это наиболее распространенный и широко используемый язык для создания запросов и определения данных. Spark SQL работает как расширение Apache Spark для обработки структурированных данных с использованием знакомого синтаксиса SQL.

Это расширение поддерживает такие языки запросов, как SQL и HiveQL. Его возможности включают привязки в Python, Scala и Java. Это позволяет запрашивать данные, хранящиеся в различных расположениях, таких как внешние базы данных, структурированные файлы данных (например, JSON) и таблицы Hive.

Пример чтения данных из CSV-файла, а не из таблицы Hive см. в статье [Выполнение интерактивных запросов в кластере HDInsight Spark](apache-spark-load-data-run-query.md).

**Выполнение запроса Spark SQL**

1. При первом запуске записной книжки некоторые задачи ядро выполняет в фоновом режиме. Дождитесь готовности ядра. Ядро будет готово, когда в записной книжке появится пустой круг рядом с именем ядра. Заполненный круг означает, что ядро занято.

    ![Запрос Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive query in HDInsight Spark")

2. Когда ядро будет готово, вставьте указанный ниже код в пустую ячейку и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код. Эта команда выводит список таблиц Hive в кластере:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    При использовании записной книжки Jupyter с кластером HDInsight Spark вы получаете предустановку `sqlContext`, которую можно применять для выполнения запросов Hive с помощью Spark SQL. `%%sql` указывает записной книжке Jupyter использовать предустановку `sqlContext` для выполнения запроса Hive. Запрос извлекает первые 10 строк из таблицы Hive (**hivesampletable**), которая по умолчанию входит в состав всех кластеров HDInsight. Для получения результатов может понадобиться около 30 секунд. Он возвращает примерно такие выходные данные: 

    ![Запрос Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive query in HDInsight Spark")

    Дополнительные сведения о `%%sql` magic и предустановленных контекстах см. в статье [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

    При каждом выполнении запроса в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок.
    
2. Выполните другой запрос, чтобы вывести данные из таблицы `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Экран обновится, и отобразятся выходные данные запроса.

    ![Выходные данные запроса Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive query output in HDInsight Spark")

2. В меню **File** (Файл) записной книжки выберите пункт **Close and Halt** (Закрыть и остановить). При завершении работы записной книжки освобождаются кластерные ресурсы.

3. Если вы планируете выполнить следующие действия позже, обязательно удалите кластер HDInsight, созданный во время работы с этой статьей. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дополнительная информация 

Из этой статьи вы узнали, как создать кластер HDInsight Spark и выполнить базовый запрос Spark SQL. Из следующей статьи вы узнаете, как с помощью кластера HDInsight Spark выполнять интерактивные запросы, используя пример данных.

> [!div class="nextstepaction"]
>[Выполнение интерактивных запросов в кластере HDInsight Spark](apache-spark-load-data-run-query.md)




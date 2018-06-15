---
title: Краткое руководство по созданию кластера Spark в HDInsight с помощью Azure PowerShell
description: В этом кратком руководстве показано, как использовать Azure PowerShell для создания кластера Apache Spark в Azure HDInsight и выполнить простой SQL-запрос Spark.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: 321f84e0d56a2bda57e1fbfa2cc562b65c6e1d30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779163"
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-powershell"></a>Краткое руководство по созданию кластера Spark в HDInsight с помощью PowerShell
Узнайте, как создать кластер Apache Spark в Azure HDInsight и как выполнять SQL-запросы Spark к таблицам Hive. Apache Spark обеспечивает быстрый анализ данных и кластерные вычисления, используя обработку в памяти. Сведения о Spark в HDInsight см. в [этой статье](apache-spark-overview.md).

В этом кратком руководстве для создания кластера Spark в HDInsight используется Azure PowerShell. Этот кластер использует Azure Storage Blob в качестве системы хранения данных кластера.

> [!IMPORTANT]
> Счета за кластеры HDInsight выставляются пропорционально за минуту независимо от их использования. Обязательно удалите кластер, когда завершите его использование. Дополнительные сведения см. в разделе [Очистка ресурсов](#clean-up-resources) этой статьи.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-hdinsight-spark-cluster"></a>Создание кластера HDInsight Spark

Создание кластера HDInsight включает в себя создание следующих объектов и ресурсов Azure.

- Группа ресурсов Azure. Группа ресурсов Azure — это контейнер для ресурсов Azure. 
- Учетная запись хранения Azure или Azure Data Lake Store.  Каждому кластеру HDInsight требуется зависимое хранилище данных. В этом кратком руководстве вы создадите учетную запись хранения.
- Кластер HDInsight другого типа.  В этом кратком руководстве вы создадите кластер Spark 2.2.

Для создания ресурсов используется сценарий PowerShell.  При запуске этого сценария вам будет предложено ввести следующие значения.

|Параметр|Значение|
|------|------|
|Имя группы ресурсов Azure | Укажите уникальное имя группы ресурсов.|
|Расположение| Укажите регион Azure, например "Центральная часть США". |
|Имя учетной записи хранения по умолчанию | Предоставьте уникальное имя учетной записи хранения. |
|Имя кластера | Укажите уникальное имя кластера Spark в HDInsight.|
|Учетные данные для входа в кластер | Используйте эту учетную запись для подключения к панели мониторинга кластера позже в данном кратком руководстве.|
|Учетные данные пользователя SSH | Можно использовать клиенты SSH для создания удаленного сеанса командной строки с кластерами HDInsight.|



1. Щелкните **Попробовать** в правом верхнем углу следующего блока кода, чтобы открыть [Azure Cloud Shell](../../cloud-shell/overview.md), и следуйте отображающимся инструкциям, чтобы подключиться к Azure.
2. Скопируйте приведенный ниже сценарий PowerShell и вставьте его в Cloud Shell. 

    ```azurepowershell-interactive
    ### Create a Spark 2.2 cluster in Azure HDInsight
        
    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"
    
    # Create an Azure storae account and container
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_LRS `
        -Location $location
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    
    # Create a Spark 2.2 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"
    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"
    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"
    
    # Default cluster size (# of worker nodes), version, type, and OS
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"
    $clusterOS = "Linux"
    
    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName
    
    # Create a blob container. This holds the default data store for the cluster.
    New-AzureStorageContainer `
        -Name $clusterName -Context $defaultStorageContext 
    
    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.2")
    
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials 
    
    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    ```
Процесс создания кластеров занимает около 20 минут. Прежде чем перейти к следующему сеансу, вы должны создать кластер.

Если при создании кластера HDInsight возникают проблемы, возможно, у вас нет необходимых разрешений. Дополнительные сведения см. в разделе [Требования к контролю доступа](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="create-a-jupyter-notebook"></a>Создание записной книжки Jupyter

Jupyter Notebook — это интерактивная среда Notebook, которая поддерживает различные языки программирования. Notebook позволяет работать с данными, объединять код с текстом Markdown и выполнять простые визуализации. 

1. Откройте [портал Azure](https://portal.azure.com).
2. Выберите **Кластеры HDInsight**, а затем выберите созданный кластер.

    ![Открытие кластера HDInsight на портале Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. На портале щелкните **Панели мониторинга кластера**, а затем — **Записная книжка Jupyter**. При появлении запроса введите учетные данные для входа в кластер.

   ![Открытие записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook to run interactive Spark SQL query")

4. Щелкните **Создать** > **PySpark**, чтобы создать элемент Notebook. 

   ![Создание записной книжки Jupyter для выполнения интерактивного запроса Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Create a Jupyter Notebook to run interactive Spark SQL queryL")

   Будет создана и открыта записная книжка с именем Untitled (Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Выполнение инструкций SQL Spark

SQL — это наиболее распространенный и широко используемый язык для создания запросов и определения данных. Spark SQL работает как расширение Apache Spark для обработки структурированных данных с использованием знакомого синтаксиса SQL.

1. Убедитесь, что ядро готово. Ядро будет готово, когда в записной книжке появится пустой круг рядом с именем ядра. Заполненный круг означает, что ядро занято.

    ![Запрос Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive query in HDInsight Spark")

    При первом запуске записной книжки некоторые задачи ядро выполняет в фоновом режиме. Дождитесь готовности ядра. 
2. Вставьте указанный ниже код в пустую ячейку и нажмите сочетание клавиш **SHIFT + ВВОД**, чтобы выполнить код. Эта команда выводит список таблиц Hive в кластере:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    При использовании записной книжки Jupyter с кластером HDInsight Spark вы получаете предустановку `sqlContext`, которую можно применять для выполнения запросов Hive с помощью Spark SQL. `%%sql` указывает записной книжке Jupyter использовать предустановку `sqlContext` для выполнения запроса Hive. Запрос извлекает первые 10 строк из таблицы Hive (**hivesampletable**), которая по умолчанию входит в состав всех кластеров HDInsight. Для получения результатов может понадобиться около 30 секунд. Он возвращает примерно такие выходные данные: 

    ![Запрос Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive query in HDInsight Spark")

    При каждом выполнении запроса в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок.
    
2. Выполните другой запрос, чтобы вывести данные из таблицы `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Экран обновится, и отобразятся выходные данные запроса.

    ![Выходные данные запроса Hive в HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive query output in HDInsight Spark")

2. Для этого в меню **File** (Файл) элемента Notebook выберите **Close and Halt** (Закрыть и остановить). При завершении работы записной книжки освобождаются кластерные ресурсы.

## <a name="clean-up-resources"></a>Очистка ресурсов
HDInsight сохраняет ваши данные в службе хранилища Azure или Azure Data Lake Store, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. Если вы планируете сразу приступить руководству, указанному в разделе [Дальнейшие действия](#next-steps), то можете оставить кластер.

Вернитесь на портал Azure и выберите **Удалить**.

![Удаление кластера HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

Кроме того, можно выбрать имя группы ресурсов, чтобы открыть страницу группы ресурсов, а затем щелкнуть **Удалить группу ресурсов**. Вместе с группой ресурсов вы также удалите кластер Spark в HDInsight и учетную запись хранения по умолчанию.

## <a name="next-steps"></a>Дополнительная информация 

В этом кратком руководстве вы узнали, как создать кластер Spark в HDInsight и выполнить базовый SQL-запрос Spark. Из следующего руководства вы узнаете, как с помощью кластера Spark в HDInsight выполнять интерактивные запросы, используя пример данных.

> [!div class="nextstepaction"]
>[Выполнение интерактивных запросов в кластерах Spark в HDInsight](./apache-spark-load-data-run-query.md)

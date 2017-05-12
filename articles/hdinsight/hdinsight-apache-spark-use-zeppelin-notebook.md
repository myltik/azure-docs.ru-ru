---
title: "Установка записных книжек Zeppelin для кластера Spark в Azure HDInsight с помощью действия сценария | Документация Майкрософт"
description: "Пошаговые инструкции по установке и использованию записных книжек Zeppelin с кластерами Spark в HDInsight на платформе Linux."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9e6e0a696ba27c7758c21fa46754a8539ae2255b
ms.contentlocale: ru-ru
ms.lasthandoff: 03/29/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>Установка записных книжек Zeppelin для кластера Apache Spark в HDInsight

Узнайте, как устанавливать записные книжки Zeppelin в кластеры Apache Spark и использовать эти записные книжки для выполнения заданий Spark.

> [!IMPORTANT]
> Если вы подготовили кластер Spark 1.6 в HDInsight 3.5, то получить доступ к Zeppelin можно по умолчанию, следуя инструкциям из статьи [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](hdinsight-apache-spark-zeppelin-notebook.md). Если вы хотите использовать Zeppelin в кластере HDInsight версий 3.3 или 3.4, то необходимо выполнить инструкции по установке Zeppelin, приведенные в этой статье.
>
> Использовать сценарии в этой статье для установки Zeppelin в кластерах Spark 2.0 невозможно.

## <a name="prerequisites"></a>Предварительные требования

* У вас должна быть подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="install-zeppelin-on-a-spark-cluster"></a>Установка Zeppelin в кластер Spark
Записную книжку Zeppelin в кластере Spark можно установить с помощью действия сценария. Действие сценария использует пользовательские скрипты для установки компонентов в кластере, которые по умолчанию недоступны. Можно использовать пользовательский сценарий для установки Zeppelin с портала Azure, воспользовавшись пакетом SDK для HDInsight .NET или Azure PowerShell. Этот сценарий можно использовать для установки Zeppelin при создании кластера или после его подготовки и запуска. Сведения о том, как это сделать, можно получить, воспользовавшись ссылками в следующих разделах.

### <a name="using-the-azure-portal"></a>Использование портала Azure
Инструкции по использованию портала Azure для выполнения действия сценария по установке записной книжки Zeppelin см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). В инструкции, приведенные в этой статье, необходимо внести несколько изменений.

* Требуется использовать сценарий для установки Zeppelin. Пользовательский сценарий для установки Zeppelin на кластер Spark в HDInsight доступен по следующим ссылкам.

  * Для кластеров Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Для кластеров Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Сценарий необходимо выполнять только на головном узле.
* Сценарий не требует никаких параметров.

### <a name="using-hdinsight-net-sdk"></a>Использование пакета HDInsight .NET SDK
Инструкции по использованию пакета SDK для .NET в HDInsight, выполняющего действие сценария по установке записной книжки Zeppelin, см. в разделе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). В инструкции, приведенные в этой статье, необходимо внести несколько изменений.

* Требуется использовать сценарий для установки Zeppelin. Пользовательский сценарий для установки Zeppelin на кластер Spark в HDInsight доступен по следующим ссылкам.

  * Для кластеров Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Для кластеров Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Сценарий не требует никаких параметров.
* В качестве типа создаваемого кластера укажите Spark.

### <a name="using-azure-powershell"></a>Использование Azure PowerShell
Представленный ниже фрагмент кода PowerShell позволяет создать кластер Spark в HDInsight Linux с установленной записной книжкой Zeppelin. В зависимости от версии кластера Spark необходимо изменить приведенный ниже фрагмент кода PowerShell, чтобы указать ссылку на соответствующий пользовательский сценарий.

* Для кластеров Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Для кластеров Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Доступ к записной книжке Zeppelin

Установив Zeppelin с помощью действия сценария, вы можете получить доступ к записной книжке Zeppelin в кластере Spark. Для этого выполните описанные ниже действия. В этом разделе показано, как выполнять инструкции %sql и %hive.

1. В веб-браузере откройте следующую конечную точку:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. Создайте новую записную книжку. На панели заголовка щелкните элемент **Notebook** (Записная книжка), а затем — **Create New Note** (Создать заметку).

    ![Создание записной книжки Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Создание записной книжки Zeppelin")

    На той же странице под заголовком **Notebook** (Записная книжка) отобразится новая записная книжка с именем, начинающимся с **Заметка XXXXXXXXX**. Щелкните новую записную книжку.
3. На веб-странице для новой записной книжки щелкните заголовок и при необходимости переименуйте записную книжку. Нажмите клавишу ВВОД, чтобы сохранить изменения. Кроме того, убедитесь, что в правом верхнем углу в заголовке записной книжки отображается состояние **Подключено** .

    ![Состояния записной книжки Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Состояния записной книжки Zeppelin")

### <a name="run-sql-statements"></a>Выполнение инструкций SQL
1. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\HdiSamples\SensorSampleData\hvac**.

    В пустой абзац, созданный по умолчанию в новой записной книжке, вставьте следующий фрагмент кода.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    Нажмите клавиши **SHIFT+ВВОД** или кнопку **воспроизведения** для абзаца, чтобы выполнить фрагмент кода. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

    ![Создание временной таблицы из необработанных данных](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Создание временной таблицы из необработанных данных")

    Можно указать заголовок для каждого абзаца. В правом верхнем углу экрана щелкните значок **Параметры**, а затем выберите элемент **Показать заголовок**.
2. Теперь вы можете выполнить инструкции Spark SQL для таблицы **hvac** . Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    Инструкция **%sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Spark SQL. Просмотреть определенные интерпретаторы вы можете на вкладке **Интерпретатор** в заголовке записной книжки.

    Выходные данные показаны на снимке экрана ниже.

    ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Выполнение инструкции Spark SQL с помощью записной книжки")

     Используйте параметры отображения (выделены красным прямоугольником) для переключения между различными представлениями одних и тех же выходных данных. Щелкните элемент **Параметры** , чтобы определить ключи и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, а среднее значение **temp_diff** — как значение.
3. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте кода показано, как определить переменную ( **Temp**) в запросе с возможными значениями, с которыми необходимо выполнить запрос. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Выходные данные показаны на снимке экрана ниже.

    ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Выполнение инструкции Spark SQL с помощью записной книжки")

    Для последующих запросов можно выбрать новое значение из раскрывающегося списка и повторно выполнить запрос. Щелкните элемент **Параметры** , чтобы определить ключи и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, среднее значение **temp_diff** используется как значение, а **targettemp** — как группа.
4. Перезапустите интерпретатор Spark SQL, чтобы выйти из приложения. Перейдите на вкладку **Интерпретатор** в верхней части окна, а затем для интерпретатора Spark нажмите кнопку **Перезапустить**.

    ![Перезапуск интерпретатора Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Перезапуск интерпретатора Zeppelin")

### <a name="run-hive-statements"></a>Выполнение инструкций Hive
1. В записной книжке Zeppelin нажмите кнопку **Interpreter** (Интерпретатор).

    ![Обновление интерпретатора Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Обновление интерпретатора Hive")
2. Для интерпретатора **Hive** нажмите кнопку **Изменить**.

    ![Обновление интерпретатора Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Обновление интерпретатора Hive")

    Обновите следующие свойства.

   * Для **default.password** задайте значение пароля, которое вы указали для администратора при создании кластера HDInsight Spark.
   * Для **default.url** задайте значение `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Замените **\<spark_cluster_name>** именем своего кластера Spark.
   * Для **default.user** задайте имя администратора, которое вы указали при создании кластера. Например, *admin*.
3. Нажмите кнопку **Сохранить**, а при появлении запроса на перезапуск интерпретатора Hive нажмите кнопку **ОК**.
4. Создайте новую записную книжку и выполните следующую инструкцию, чтобы получить полный список всех таблиц Hive в кластере.

        %hive
        SHOW TABLES

    По умолчанию кластер HDInsight содержит образец таблицы с именем **hivesampletable** , поэтому должны отобразиться следующие выходные данные.

    ![Выходные данные Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Выходные данные Hive")
5. Выполните следующую инструкцию, чтобы получить список записей в таблице.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Должен отобразиться следующий результат.

    ![Выходные данные Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Выходные данные Hive")

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely on HDInsight Spark Linux cluster (Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md


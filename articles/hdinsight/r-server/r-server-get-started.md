---
title: Приступая к работе с R Server в HDInsight в Azure | Документация Майкрософт
description: Узнайте, как создать Apache Spark в кластере HDInsight, который содержит R Server, и отправить скрипт R в кластер.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: f4265ce7370542d8253222a5e268ea9cde7fd36e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Начало работы с кластером R Server в Azure HDInsight

Azure HDInsight поддерживает интеграцию R Server в кластер HDInsight. Эта поддержка позволяет скриптам R использовать MapReduce и Spark для выполнения распределенных вычислений. Из этой статьи вы узнаете, как создать Microsoft R Server в кластере HDInsight. Также вы узнаете, как запустить скрипт R с примерами использования Spark для распределенных вычислений R.


## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Этот процесс описан в видеоролике [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Как получить бесплатную пробную версию Azure для тестирования Hadoop в HDInsight).
* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Создание кластера с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **Создать ресурс** > **Данные и аналитика** > **HDInsight**.

3. В колонке **Основные сведения** задайте следующие параметры:

    * **Имя кластера.** Имя кластера HDInsight.
    * **Подписка.** Выберите нужную подписку.
    * **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Имя для входа в кластер по протоколу HTTPS. Эти учетные данные используются для доступа к таким службам, как веб-интерфейс Ambari или REST API.
    * **Secure Shell (SSH) username** (Имя пользователя Secure Shell (SSH)). Имя для доступа к кластеру по протоколу SSH. По умолчанию пароль совпадает с паролем для входа в кластер.
    * **Группа ресурсов.** Группа ресурсов, в которой будет создан кластер.
    * **Расположение.** Регион Azure, в котором будет создан кластер.

        ![Основные сведения о кластере](./media/r-server-get-started/clustername.png)

4. Выберите **тип кластера** и в разделе **Конфигурация кластера** задайте следующие значения:

    * **Тип кластера**: R Server.

    * **Операционная система**: Linux.

    * **Версия**: R Server 9.1 (HDI 3.6). Заметки о выпуске для доступных версий R Server см. на сайте [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **Выпуск R Studio Community для R Server**: эта среда IDE с браузерным интерфейсом устанавливается по умолчанию на граничном узле. Если вы не хотите ее устанавливать, снимите соответствующий флажок. Если вы решили установить среду, на портале в колонке приложения для созданного кластера вы найдете URL-адрес для входа в RStudio Server.

        ![Основные сведения о кластере](./media/r-server-get-started/clustertypeconfig.png)

4. Выбрав тип кластера, нажмите кнопку __Выбрать__, чтобы установить выбранный тип. Затем нажмите кнопку __Далее__, чтобы завершить настройку основных параметров.

5. В разделе **Хранилище** выберите или создайте учетную запись хранения. Для выполнения действий, описанных в этом документе, в других полях этого раздела оставьте значения по умолчанию. Нажмите кнопку __Далее__, чтобы сохранить конфигурацию хранилища.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/cluster-storage.png)

6. В разделе **Сводка** просмотрите конфигурацию для кластера. Используйте ссылки __Изменить__, чтобы изменить неправильные параметры. Наконец, нажмите кнопку __Создать__ для создания кластера.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Подключение к RStudio Server

Если вы решили установить HDInsight RStudio Server Community Edition как часть кластера, вы можете войти в RStudio двумя способами.

* **Вариант 1.** Откройте в браузере следующий URL-адрес (где **CLUSTERNAME** — это имя созданного кластера R Server):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Вариант 2.** Откройте кластер R Server на портале Azure. В разделе **Быстрые ссылки** щелкните **Панели мониторинга R Server**.

     ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    В разделе **Панели мониторинга кластера** выберите **R Studio Server**.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > В любом случае при первом входе понадобится выполнить аутентификацию дважды.  При первом запросе на аутентификацию укажите *имя пользователя* и *пароль* администратора кластера. При втором запросе на аутентификацию укажите *идентификатор пользователя SSH* и *пароль*. При последующих операциях входа вам нужно будет указывать только учетные данные SSH.

После подключения ваш экран будет похож на этот снимок экрана:

![Подключение к RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Запуск примера задания

Вы можете отправлять задания с помощью функций scaleR. Ниже приведен пример команды для выполнения задания:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Подключение к пограничному узлу кластера

Из этого раздела вы узнаете, как подключиться к граничному узлу кластера R Server HDInsight с помощью SSH. Сведения об использовании SSH см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Команда SSH для подключения к граничному узлу кластера R Server выглядит так:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Чтобы найти команду SSH для кластера, на портале Azure щелкните имя кластера, выберите **SSH + Cluster login** (SSH + вход в кластер), а затем для параметра **Имя узла** выберите граничный узел. Так можно отобразить информацию о конечной точке SSH для пограничного узла.

![Изображение конечной точки SSH для пограничного узла](./media/r-server-get-started/sshendpoint.png)

Если для защиты учетной записи SSH используется пароль, то предлагается ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i`, чтобы указать соответствующий закрытый ключ. Например: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Когда подключение будет установлено, отобразится окно запроса, как показано ниже:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>Использование консоли R Server

1. В сеансе SSH используйте следующую команду, чтобы запустить консоль R:  

        R

2. Отобразятся выходные данные с версией R Server, а также другие сведения.
    
3. В строке ввода `>` можно ввести код R. R Server в HDInsight содержит пакеты, которые позволяют легко взаимодействовать с Hadoop и выполнять распределенные вычисления. Например, используйте следующую команду, чтобы просмотреть корень файловой системы по умолчанию для кластера HDInsight.

        rxHadoopListFiles("/")

4. Также можно использовать WASB-адресацию.

        rxHadoopListFiles("wasb:///")

5. Закройте консоль R с помощью следующей команды:

        quit()

## <a name="automated-cluster-creation"></a>Автоматизированное создание кластера

Вы можете автоматизировать создание кластера R Server в HDInsight с помощью шаблонов Azure Resource Manager, пакета SDK или PowerShell.

* Дополнительные сведения о создании кластера R Server с помощью шаблона Azure Resource Manager см. в статье о [развертывании кластера R Server HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Сведения о создании кластера R Server с помощью пакета SDK для .NET см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Чтобы создать кластер R Server, с помощью PowerShell, см. статью [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как создать новый кластер R Server в Azure HDInsight и как использовать консоль R в сеансе SSH. В следующих статьях описаны другие способы использования и администрирования R Server в HDInsight:

* [Отправка заданий из расширения "Инструменты R для Visual Studio"](r-server-submit-jobs-r-tools-vs.md)
* [Manage R Server cluster on HDInsight](r-server-hdinsight-manage.md) (Управление кластером R Server в HDInsight)
* [Operationalize R Server cluster on HDInsight](r-server-operationalize.md) (Эксплуатация кластера R Server в HDInsight)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Решения службы хранилища Azure для R Server в HDInsight](r-server-storage.md)

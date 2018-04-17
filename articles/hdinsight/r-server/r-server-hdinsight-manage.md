---
title: Управление кластером R Server в Azure HDInsight | Документация Майкрософт
description: Узнайте, как управлять кластером R Server в Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c0a996555e35a99a6025e92bcb41fa192b18eece
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Управление кластером R Server в Azure HDInsight

Из этой статьи вы узнаете, как управлять существующим кластером R Server в Azure HDInsight, и научитесь выполнять такие задачи, как добавление нескольких одновременных пользователей, удаленное подключение к серверу или клиенту R Server (Microsoft ML Server), изменение контекста вычислений и т. д.

## <a name="prerequisites"></a>предварительным требованиям

* **Кластер R Server в HDInsight**. Инструкции см. в статье [Начало работы с R Server в HDInsight](r-server-get-started.md).

* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Включение нескольких параллельных подключений пользователей

Вы можете разрешить несколько параллельных подключений к R Server в HDInsight, добавив дополнительных пользователей на граничном узле, на котором выполняется версия RStudio Community. При создании кластера HDInsight необходимо указать двух пользователей: для HTTP и SSH.

![Параллельное подключение пользователя 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Имя пользователя для входа в кластер** — пользователь HTTP для проверки подлинности с помощью шлюза HDInsight, который применяется для защиты созданных вами кластеров HDInsight. Этот пользователь HTTP служит для доступа к пользовательскому интерфейсу Ambari, пользовательскому интерфейсу YARN, а также другим компонентам пользовательского интерфейса.
- **Имя пользователя Secure Shell (SSH)** — пользователь SSH для доступа к кластеру через безопасную оболочку. Этот пользователь применяется в системе Linux для всех головных, рабочих и граничных узлов. Таким образом, с помощью безопасной оболочки вы можете получить доступ к любому узлу в удаленном кластере.

Версия R Studio Server Community, которая используется в кластере Microsoft R Server в HDInsight, поддерживает только один метод входа: имя пользователя и пароль Linux. Эта версия не поддерживает передачу токенов. Таким образом, при первом входе в R Studio в кластере R Server вам потребуется ввести учетные данные два раза.

- Сначала используйте учетные данные пользователя HTTP через шлюз HDInsight. 

- Затем используйте учетные данные SSH для входа в RStudio.
  
Сейчас при подготовке кластера HDInsight можно создать только одну учетную запись пользователя SSH. Поэтому, чтобы предоставить нескольким пользователям доступ к кластеру R Server в HDInsight, необходимо создать дополнительных пользователей в системе Linux.

Так как RStudio работает на граничном узле кластера, нужно выполнить несколько дополнительных шагов:

1. войдите на граничный узел с учетными данными существующего пользователя SSH;
2. добавить нескольких пользователей Linux на граничный узел;
3. использовать версию RStudio Community с помощью созданного пользователя.

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Шаг 1. Вход на граничный узел с помощью созданного пользователя SSH

Чтобы войти на граничный узел, следуйте инструкциям по [подключению к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Адрес граничного узла кластера R Server в HDInsight: `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Шаг 2. Добавление дополнительных пользователей Linux на граничный узел

Чтобы добавить пользователя на граничный узел, выполните эти команды:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user 
    sudo passwd <yournewusername>

Выходные данные показаны на снимке экрана ниже.

![Параллельное подключение пользователя 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

При появлении запроса на ввод текущего пароля Kerberos нажмите клавишу **ВВОД**, чтобы игнорировать его. Параметр `-m` в команде `useradd` указывает, что система создаст домашнюю папку пользователя, которая обязательна для версии RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Шаг 3. Использование версии RStudio Community с помощью созданного пользователя

Войдите в RStudio из https://CLUSTERNAME.azurehdinsight.net/rstudio/. Если вы впервые входите в систему после создания кластера, сначала введите учетные данные администратора кластера, а затем только что созданные учетные данные пользователя SSH. Если это уже не первый вход, достаточно ввести учетные данные нового пользователя SSH.

Вы также можете войти параллельно с помощью исходных учетных данных (по умолчанию — *sshuser*) в другом окне браузера.

Обратите внимание, что у добавленных пользователей нет привилегированных прав в системе Linux, но у них есть тот же доступ ко всем файлам в удаленном хранилище HDFS и WASB.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Удаленное подключение к серверу или клиенту Microsoft ML Server

Вы можете получить доступ к контексту вычислений Hadoop Spark в HDInsight из удаленного экземпляра Microsoft R Server или Microsoft R Client, запущенного на персональном компьютере. Для этого укажите на ноутбуке параметры dfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches и sshProfileScript, определяя контекст вычислений RxSpark.

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Дополнительные сведения см. в разделе об использовании Microsoft R Server в качестве клиента Hadoop в статье [How to use RevoScaleR in a Spark compute context](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Как использовать RevoScaleR в контексте вычислений Spark).

## <a name="use-a-compute-context"></a>Использование контекста вычислений

Контекст вычислений позволяет определить, где будут выполнены вычисления — на локальном компьютере или пограничном узле, или же они будут распределены между узлами в кластере HDInsight.

1. В RStudio Server или консоли R (в сеансе SSH) выполните следующий код, чтобы загрузить пример данных в хранилище по умолчанию для HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Теперь создайте сведения о данных и определите два источника данных, чтобы можно было работать с данными.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Выполните логистическую регрессию данных с помощью локального контекста вычислений.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    В результате вы должны получить строки, аналогичные приведенным ниже:

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Теперь примените ту же логистическую регрессию в контексте Spark. Контекст Spark распределит обработку между всеми рабочими узлами в кластере HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Кроме того, для распределения вычислений между узлами кластера можно использовать MapReduce. Дополнительные сведения о контексте вычислений см. в статье с описанием [параметров контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Распространение кода R на несколько узлов

Используя R Server в HDInsight, вы можете выполнить имеющийся код R на нескольких узлах кластера с помощью `rxExec`. Эта функция удобна при очистке параметров или моделировании. Следующий код является примером использования `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Если вы все еще используете контекст Spark или MapReduce, эта команда вернет значение nodename рабочих узлов, на которых запущен код (`(Sys.info()["nodename"])`). Например, для кластера с четырьмя узлами результат будет аналогичен приведенному ниже:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Доступ к данным в Hive и Parquet

Возможность, реализованная в версии R Server 9.1, обеспечивает прямой доступ к данным в Hive и Parquet для использования функций ScaleR в контексте вычислений Spark. Эти возможности, доступные в рамках новых функций источника данных ScaleR, называются RxHiveData и RxParquetData. Они используются со Spark SQL для загрузки данных непосредственно в Spark DataFrame и последующего анализа с помощью ScaleR.  

Ниже представлен пример кода с использованием новых функций:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Дополнительные сведения об использовании этих новых функций см. в справочной документации по ML Server (доступна с помощью команд `?RxHivedata` и `?RxParquetData`).  

## <a name="install-additional-r-packages-on-the-cluster"></a>Установка дополнительных пакетов R в кластер

### <a name="to-install-r-packages-on-the-edge-node"></a>Установка пакетов R на граничный узел

Если вы хотите установить дополнительные пакеты R на граничный узел, подключитесь к граничному узлу по протоколу SSH и вызовите `install.packages()` непосредственно из консоли R. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Установка пакетов R на рабочий узел

Чтобы установить пакеты R на рабочие узлы кластера, необходимо использовать действие скрипта. Действия скриптов — это скрипты Bash, которые используются для изменения конфигурации кластера HDInsight или установки дополнительного программного обеспечения, например дополнительных пакетов R. 

> [!IMPORTANT]
> Использовать действия сценария для установки дополнительных пакетов R можно только после создания кластера. Не используйте процедуру во время создания кластера, так как скрипту требуется полностью установленный и настроенный R Server.
>
>

1. Выполните действия по [настройке кластеров с помощью действия скрипта](../hdinsight-hadoop-customize-cluster-linux.md).

3. В разделе **Отправка действия скрипта** введите следующие сведения.

   * В поле **Тип скрипта** выберите **Пользовательский**.

   * В поле **Имя** укажите имя для действия скрипта.

    * В поле **URI bash-скрипта** введите `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Этот скрипт устанавливает дополнительные пакеты R на рабочий узел.

   * Установите флажок только для параметра **Рабочая роль**.

   * **Параметры**: устанавливаемые пакеты R. Например, `bitops stringr arules`

   * Установите флажок **Persist this script action** (Сохранить это действие скрипта).  

   > [!NOTE]
   > 1. По умолчанию все пакеты R устанавливаются из моментального снимка репозитория Microsoft MRAN, согласованного с версией сервера R Server, которая была установлена. Если вы хотите установить более новые версии пакетов, существует риск несовместимости. Но эту установку можно выполнить, указав `useCRAN` в качестве первого элемента список пакетов, например `useCRAN bitops, stringr, arules`.  
   > 2. Для некоторых пакетов R требуются дополнительные системные библиотеки Linux. Для удобства мы предварительно установили зависимости, необходимые для 100 наиболее популярных пакетов R. Однако если для устанавливаемых пакетов R требуются дополнительные библиотеки, то необходимо скачать основной скрипт, используемый в этой статье, и добавить шаги для установки системных библиотек. Затем необходимо передать измененный сценарий в общедоступный контейнер больших двоичных объектов в службе хранилище Azure и использовать этот измененный сценарий для установки пакетов.
   >    Дополнительные сведения см. в статье [Разработка действий сценариев с помощью HDInsight](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Добавление действия сценария](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Нажмите кнопку **Создать**, чтобы выполнить скрипт. После завершения выполнения пакеты R будут доступны на всех рабочих узлах.

## <a name="next-steps"></a>Дополнительная информация

* [Operationalize R Server cluster on Azure HDInsight](r-server-operationalize.md) (Ввод в эксплуатацию кластера R Server в Azure HDInsight)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Решения службы хранилища Azure для R Server в HDInsight](r-server-storage.md)

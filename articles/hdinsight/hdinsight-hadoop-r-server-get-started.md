<properties
   pageTitle="Приступая к работе с R Server в HDInsight (предварительная версия) | Azure"
   description="Узнайте, как создать Apache Spark в кластере HDInsight (Hadoop), который содержит R Server (предварительная версия), а затем отправить сценарий R для кластера."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# Приступая к работе с R Server в HDInsight (предварительная версия)

Предложение уровня "Премиум" для HDInsight включает в себя R Server в составе кластера HDInsight (предварительная версия). Это позволяет сценариям R использовать MapReduce и Spark для выполнения распределенных вычислений. В этом документе вы узнаете, как создать новый R Server в HDInsight, а затем запустите сценарий R, демонстрирующий использование Spark для распределенных вычислений на R.

![Схема рабочего процесса для этого документа](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Предварительные требования

* __Подписка Azure__. Прежде чем приступать к изучению этого учебника, необходимо оформить подписку Azure. Подробнее об этом см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* __Клиент Secure Shell (SSH)__. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. В системах Linux, Unix и OS X клиент SSH предоставляется с помощью команды `ssh`. Для систем Windows рекомендуем использовать [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Ключи SSH (необязательно)__. Для защиты учетной записи SSH, которая используется для подключения к кластеру, можно применять пароль или открытый ключ. Использовать пароль проще, он позволяет приступить к работе, не создавая пару открытого и закрытого ключей. Тем не менее использование ключа является более безопасным.
    
        В инструкциях в этом документе предполагается, что вы используете пароль. Информацию о том, как создать и использовать ключи SSH в HDInsight, см. в следующих документах.
        
        * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Создание кластера

> [AZURE.NOTE] Действия, описанные в этом документе, позволяют создать R Server в HDInsight с помощью основных данных конфигурации. Сведения о других параметрах конфигурации кластера (например, для добавления учетных записей хранения, использования виртуальной сети Azure или создания метахранилища для Hive) см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Войдите на [портал Azure](https://portal.azure.com).

2. Последовательно щелкните __Создать__, __Данные+аналитика__ и __HDInsight__.

    ![Изображение, на котором показано создание нового кластера](./media/hdinsight-getting-started-with-r/newcluster.png)

3. В поле __Имя кластера__ введите имя кластера. Если у вас несколько подписок Azure, с помощью элемента __Подписка__ выберите ту, которую хотите использовать.

    ![Выбор имени кластера и подписки](./media/hdinsight-getting-started-with-r/clustername.png)

4. Щелкните __Выберите тип кластера__. В колонке __Тип кластера__ выберите следующие параметры.

    * __Тип кластера__: "R Server в Spark";
    
    * __Уровень кластера__: "Премиум".

    Для других параметров оставьте значения по умолчанию. Затем с помощью кнопки __Выбрать__ сохраните тип кластера.
    
    ![Снимок экрана колонки "Тип кластера"](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] Можно также добавить R Server в кластеры HDInsight других типов (например, Hadoop и HBase), выбрав этот тип кластера, а затем щелкнув __Премиум__.

5. Щелкните **Группа ресурсов**, чтобы просмотреть список существующих групп ресурсов и выбрать ту, в которой будет создан кластер. Вы также можете щелкнуть **Создать** и ввести имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

    > [AZURE.NOTE] Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).
    
    С помощью кнопки __Выбрать__ сохраните группу ресурсов.

6. Выберите **Учетные данные**, затем введите **Имя пользователя кластера** и **Пароль кластера**.

    Введите __имя пользователя SSH__. Протокол SSH используется для удаленного подключения к кластеру с помощью клиента __Secure Shell (SSH)__. Пользователя SSH можно указать в этом диалоговом окне или на вкладке "Конфигурация" после создания кластера. В качестве __имени пользователя SSH__ для R Server необходимо указать значение remoteuser. Если используется другое имя, после создания кластера потребуется выполнить дополнительное действие.
    
    ![Колонка учетных данных](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __Тип проверки подлинности SSH__. Если не требуется использовать открытый ключ, выберите для этого параметра значение __ПАРОЛЬ__. Для доступа к R Server в кластере через удаленный клиент (например, RTVS, RStudio или другой классический интерфейс IDE) требуется пара открытого и закрытого ключей.

	Чтобы создать и использовать их, для параметра "Тип проверки подлинности SSH" выберите значение "ОТКРЫТЫЙ КЛЮЧ" и выполните приведенные ниже действия. При выполнении этих указаний предполагается, что установлена среда Cygwin с программой ssh-keygen или эквивалентным средством.

	-    Создайте пару открытого и закрытого ключей с помощью командной строки на ноутбуке:
	  
		    ssh-keygen -t rsa -b 2048 –f <private-key-filename>
      
    -    В результате будет создан файл закрытого и открытого ключей с именем <имя\_файла\_закрытого\_ключа >.pub (например, davec или davec.pub). Затем при назначении учетных данных кластера HDI укажите файл открытого ключа (PUB-файл).
      
		![Колонка учетных данных](./media/hdinsight-getting-started-with-r/publickeyfile.png)
      
	-    Измените разрешения в файле закрытого ключа на ноутбуке:
      
			chmod 600 <private-key-filename>
      
	-    Используйте файл закрытого ключа и SSH для удаленного входа в систему. Пример:
	  
			ssh –i <private-key-filename> remoteuser@<hostname public ip>
      
	  Кроме того, его можно использовать в клиенте как часть определения контекста вычислений Hadoop Spark для R Server (дополнительные сведения см. в подразделе об использовании Microsoft R Server в качестве клиента Hadoop раздела [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Создание контекста вычислений для Spark) [руководства по началу работы с функцией RevoScaleR в Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).

7. Щелкните **Источник данных**, чтобы выбрать источник данных для кластера. Щелкнув __Выберите учетную запись хранения__, выберите имеющуюся учетную запись или создайте новую с помощью ссылки __Создать__ в разделе __Выберите учетную запись хранения__.

    При использовании ссылки __Создать__ необходимо ввести имя новой учетной записи хранения. Если имя допустимо, появится зеленый флажок.

    __Контейнер по умолчанию__ получит имя кластера по умолчанию. Оставьте это значение.
    
    Щелкните __Расположение__, чтобы выбрать регион для создания учетной записи хранения.
    
    > [AZURE.IMPORTANT] Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

    Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию источника данных.
    
    ![Колонка "Источник данных"](./media/hdinsight-getting-started-with-r/datastore.png)

8. Выберите **Ценовые категории узла**, чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Если вы считаете, что вам не требуется больший размер кластера, оставьте число рабочих узлов по умолчанию: `4`. Оценочная стоимость кластера отобразится в колонке.

	> [AZURE.NOTE] Если требуется, то на портале можно изменить количество рабочих узлов в кластере ("Кластер" -> "Параметры" -> "Изменить масштаб кластера"). Таким образом, кластер можно перевести в состояние ожидания, когда он не используется, или увеличить его емкость для выполнения масштабных заданий.

	При изменении размера кластера, граничного узла и числа узлов данных необходимо учитывать следующие факторы:
   
    - Если объем данных большой, производительность распределенных анализов R Server в Spark пропорциональна количеству рабочих узлов.
    - Производительность анализов R Server линейно зависит от размера анализируемых данных. Например:
        - Если объем данных небольшой или средний, для оптимальной производительности анализ данных необходимо выполнять в рамках локального контекста вычислений на граничном узле. Дополнительные сведения об использовании локального контекста вычислений и контекста вычислений Spark см. в статье "Параметры контекста вычислений для R Server в HDInsight (предварительная версия)".<br>
        - Если войти на граничный узел и запустить там R-скрипт, все RX-функции, кроме RX-функций ScaleR, будут выполняться на нем <strong>локально</strong>. Поэтому необходимо соответствующим образом задать объем памяти и количество ядер граничного узла. Эти же рекомендации применимы при использовании на ноутбуке сервера R Server в HDI в качестве удаленного контекста вычислений.
    
    ![Колонка "Ценовые категории узла"](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию цен узла.
    
9. В колонке **Новый кластер HDInsight** обязательно выберите параметр **Закрепить на начальной панели**, а затем нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется создание кластера. После завершения создания вместо него будет отображаться значок HDInsight.

    | При создании | Создание завершено |
    | ------------------ | --------------------- |
    | ![Индикатор подготовки на начальной панели](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Плитка создания кластера](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] Обычно создание кластера занимает около 15 минут. Вы можете отслеживать процесс создания с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.

## Подключение к пограничному узлу R Server

Подключиться к пограничному узлу R Server кластера HDInsight можно с помощью SSH.

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] Адрес `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` также можно найти на портале Azure, выбрав кластер и последовательно щелкнув __Все параметры__, __Приложения__, __RServer__. Так можно отобразить информацию о конечной точке SSH для пограничного узла.
>
> ![Изображение конечной точки SSH для пограничного узла](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Если для защиты учетной записи SSH используется пароль, будет предложено ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i`, чтобы указать соответствующий закрытый ключ. Например, `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`.
    
Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

После установления подключения отобразится окно запроса, как показано ниже.

    username@ed00-myrser:~$

## Использование консоли R

1. В сеансе SSH используйте следующую команду, чтобы запустить консоль R.

        R
    
    Должен появиться результат, аналогичный приведенному ниже.
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. В строке ввода `>` можно ввести код R. R Server содержит пакеты, которые позволяют легко взаимодействовать с Hadoop и выполнять распределенные вычисления. Например, используйте следующую команду, чтобы просмотреть корень файловой системы по умолчанию для кластера HDInsight.

        rxHadoopListFiles("/")
    
    Также можно использовать WASB-адресацию.
    
        rxHadoopListFiles("wasbs:///")

## Использование R Server в HDI из удаленного экземпляра Microsoft R Server или клиента Microsoft R Client

Для доступа к кластеру можно использовать пару открытого и закрытого ключей, как описано в одном из предыдущих разделов. Кроме того, доступ к контексту вычислений Hadoop Spark в HDI можно установить из удаленного экземпляра Microsoft R Server или клиента Microsoft R Client, запущенного на настольном компьютере или ноутбуке (дополнительные сведения см. в подразделе об использовании Microsoft R Server в качестве клиента Hadoop раздела [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Создание контекста вычислений для Spark) [руководства по началу работы с функцией RevoScaleR в Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)). Для этого при определении контекста вычислений RxSpark на ноутбуке необходимо указать следующие параметры: dfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches и sshProfileScript. Например:

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
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

    
 
## Использование контекста вычислений

Контекст вычислений позволяет определить, где будут выполнены вычисления — на локальном компьютере или пограничном узле, или же они будут распределены между узлами в кластере HDInsight.
        
1. В консоли R выполните следующую команду, чтобы загрузить пример данных в хранилище по умолчанию для HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

2. Теперь давайте создадим сведения о данных и определим два источника данных, чтобы можно было работать с данными.

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

3. Выполним логистическую регрессию данных с помощью локального контекста вычислений.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    В результате вы должны получить строки, аналогичные приведенным ниже.

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

4. Теперь выполним ту же логистическую регрессию, используя контекст Spark. Контекст Spark распределит обработку между всеми рабочими узлами в кластере HDInsight.

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

    > [AZURE.NOTE] Кроме того, для распределения вычислений между узлами кластера можно использовать MapReduce. Дополнительные сведения о контексте вычислений см. в статье [Параметры контекста вычислений для R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-compute-contexts.md).

## Распространение кода R на несколько узлов

Используя R Server, вы можете с легкостью взять имеющийся код R и запустить его на нескольких узлах кластера с помощью `rxExec`. Это удобно при очистке параметров или моделировании. Ниже приведен пример использования `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Если вы все еще используете контекст Spark или MapReduce, этот метод вернет значение nodename рабочих узлов, на которых запущен код (`Sys.info()["nodename"]`). Например, для кластера с четырьмя узлами результат будет аналогичен приведенному ниже.

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

## Установка пакетов R

Если вы хотите установить дополнительные пакеты R на граничный узел, то можете использовать `install.packages()` непосредственно из консоли R при подключении к граничному узлу по протоколу SSH. Но если требуется установить пакеты R на рабочие узлы кластера, необходимо использовать действие сценария.

Действия сценария — это сценарии Bash, которые используются для изменения конфигурации кластера HDInsight или установки дополнительного программного обеспечения. В данном случае они используются для установки дополнительных пакетов R. Чтобы установить дополнительные пакеты с помощью действия сценария, выполните следующее.

> [AZURE.IMPORTANT] Использовать действия сценария для установки дополнительных пакетов R можно только после создания кластера. Их не следует использовать во время создания кластера, так как сценарию требуется полностью установленный и настроенный R Server.

1. На [портале Azure](https://portal.azure.com) выберите свой R Server в кластере HDInsight.

2. В колонке кластера выберите __Все параметры__, а затем — __Действия скрипта__. В колонке __Действия скрипта__ выберите __Отправить новое__, чтобы отправить новое действие скрипта.

    ![Изображение колонки "Действия скрипта"](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. В колонке __Отправка действия скрипта__ введите следующие сведения:

  - __Имя__: понятное имя для идентификации этого сценария.
  - __URI bash-скрипта__: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`.
  - __Головной__: флажок должен быть __снят__.
  - __Рабочая роль__: флажок должен быть __установлен__.
  - __Zookeeper__: флажок должен быть __снят__.
  - __Параметры__: устанавливаемые пакеты R. Например, `bitops stringr arules`
  - __Сохранить этот скрипт…__: флажок должен быть __установлен__.

    > [AZURE.NOTE] 1. По умолчанию все пакеты R устанавливаются из моментального снимка репозитория Microsoft MRAN, согласованного с версией сервера R Server, которая была установлена. Если вы хотите установить более новые версии пакетов, то существует определенный риск несовместимости. Это возможно, если указать `useCRAN` в качестве первого элемента в списке пакетов, например: `useCRAN bitops, stringr, arules`.
    > 2. Для некоторых пакетов R потребуются дополнительные системные библиотеки Linux. Для удобства мы предварительно установили зависимости, необходимые для 100 наиболее популярных пакетов R. Однако, если для устанавливаемых пакетов R требуются дополнительные библиотеки, то необходимо скачать основной сценарий, используемый в этой статье, и добавить шаги для установки системных библиотек. Затем необходимо передать измененный сценарий в общедоступный контейнер больших двоичных объектов в службе хранилище Azure и использовать этот измененный сценарий для установки пакетов. Дополнительные сведения о разработке действий сценариев см. в статье [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).

    ![Добавление действия сценария](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Нажмите кнопку __Создать__, чтобы выполнить сценарий. После завершения выполнения пакеты R будут доступны на всех рабочих узлах.
    
## Дальнейшие действия

Теперь, когда вы знаете, как создать кластер HDInsight, который включает R Server, а также основные принципы использования консоли R в сеансе SSH, узнайте другие способы работы с R Server в HDInsight в следующих статьях.

- [Add RStudio Server to HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Compute context options for R Server on HDInsight premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure Storage options for R Server on HDInsight premium](hdinsight-hadoop-r-server-storage.md)

### Шаблоны диспетчера ресурсов Azure

Если вы заинтересованы в автоматизации создания R Server в HDInsight с помощью шаблонов Azure Resource Manager, ознакомьтесь со следующими примерами шаблонов.

* [Create an R Server on HDInsight cluster using an SSH public key](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Create an R Server on HDInsight cluster using an SSH password](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Оба шаблона создают новый кластер HDInsight и связанную с ним учетную запись хранения. Их можно использовать с помощью Azure CLI, Azure PowerShell или портала Azure.

Общую информацию об использовании шаблонов Azure Resource Manager см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0921_2016-->
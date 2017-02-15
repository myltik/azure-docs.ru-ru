---
title: "Приступая к работе с R Server в HDInsight | Документация Майкрософт"
description: "Узнайте, как создать Apache Spark в кластере HDInsight, который содержит R Server, а затем отправить скрипт R в кластер."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 888a818bc0ea1804366dcb3932ac0e3f7b5802c6
ms.openlocfilehash: 51341d933525ef77d1f9ab604b212854b447ebc5


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Приступая к работе с R Server в HDInsight
HDInsight поддерживает интеграцию R Server в кластер HDInsight. Это позволяет сценариям R использовать MapReduce и Spark для выполнения распределенных вычислений. В этом документе вы узнаете, как создать новый R Server в кластере HDInsight, а затем запустите сценарий R, демонстрирующий использование Spark для распределенных вычислений на R.

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. См. дополнительные сведения о [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. В системах Linux, Unix и OS X клиент SSH предоставляется с помощью команды `ssh`. Для систем Windows рекомендуем использовать [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

  * **Ключи SSH (необязательно)**. Для защиты учетной записи SSH, которая используется для подключения к кластеру, можно применять пароль или открытый ключ. Использовать пароль проще, он позволяет приступить к работе, не создавая пару открытого и закрытого ключей. Тем не менее использование ключа является более безопасным.

      В инструкциях в этом документе предполагается, что вы используете пароль. Информацию о том, как создать и использовать ключи SSH в HDInsight, см. в следующих документах.

    * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Использование SSH с HDInsight из клиентов Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Создание кластера
> [!NOTE]
> Действия, описанные в этом документе, помогут создать R Server в кластере HDInsight с помощью основных данных конфигурации. Сведения о других параметрах конфигурации кластера (например, для добавления учетных записей хранения, использования виртуальной сети Azure или создания хранилища метаданных для Hive) см. в статье, посвященной [созданию кластеров HDInsight на платформе Linux](hdinsight-hadoop-provision-linux-clusters.md).
>
> 

1. Войдите на [портал Azure](https://portal.azure.com).

2. Щелкните **Создать**, **Аналитика** и **HDInsight**.

    ![Изображение, на котором показано создание нового кластера](./media/hdinsight-getting-started-with-r/newcluster.png)

3. В поле **Имя кластера** введите имя кластера. Если у вас несколько подписок Azure, с помощью элемента **Подписка** выберите нужную.

    ![Выбор имени кластера и подписки](./media/hdinsight-getting-started-with-r/clustername.png)

4. Щелкните **Конфигурация кластера**. В колонке **Конфигурация кластера** выберите следующие параметры.

   * **Тип кластера**: R Server.
   * **Версия**: выберите версию R Server для установки в кластере. Установите последнюю версию, чтобы использовать новые функции. Доступны и другие версии, если это необходимо для обеспечения совместимости. Заметки о выпуске для каждой из доступных версий см. [здесь](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **Выпуск R Studio Community для R Server**: эта среда IDE на основе браузера устанавливается по умолчанию на граничном узле.  Если вы не хотите устанавливать ее, просто снимите флажок. Если же вы решили установить среду, на портале в колонке приложения для созданного кластера вы найдете URL-адрес для входа в RStudio Server.
     Для других параметров оставьте значения по умолчанию. Затем с помощью кнопки **Выбрать** сохраните тип кластера.

     ![Снимок экрана колонки "Тип кластера"](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

   

5. Щелкните **Учетные данные**, а затем введите **имя пользователя** и **пароль** для входа в кластер.

    Введите **имя пользователя SSH**.  Протокол SSH используется для удаленного подключения к кластеру с помощью клиента **Secure Shell (SSH)**. Пользователя SSH можно указать в этом диалоговом окне или на вкладке "Конфигурация" после создания кластера. В качестве **имени пользователя SSH** для R Server необходимо указать значение remoteuser.  Если используется другое имя, после создания кластера потребуется выполнить дополнительное действие.

    ![Колонка учетных данных](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    **Тип проверки подлинности SSH**. Если не требуется использовать открытый ключ, выберите для этого параметра значение **Пароль**.  Для доступа к R Server в кластере через удаленный клиент (например, RTVS, RStudio или другой классический интерфейс IDE) требуется пара открытого и закрытого ключей. Обратите внимание: при установке выпуска RStudio Server Community необходимо выбрать пароль SSH.     

    Чтобы создать и использовать их, для параметра "Тип проверки подлинности SSH" выберите значение "ОТКРЫТЫЙ КЛЮЧ" и выполните приведенные ниже действия.  При выполнении этих указаний предполагается, что установлена среда Cygwin с программой ssh-keygen или эквивалентным средством.

   * Создайте пару открытого и закрытого ключей с помощью командной строки на ноутбуке:

        ssh-keygen -t rsa -b 2048 –f <имя_файла_закрытого_ключа>

   * В результате будет создан файл закрытого и открытого ключей с именем <имя_файла_закрытого_ключа >.pub (например, davec или davec.pub).  Затем при назначении учетных данных кластера HDI укажите файл открытого ключа (PUB-файл).

     ![Колонка учетных данных](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Измените разрешения в файле закрытого ключа на ноутбуке:

        chmod 600 <имя_файла_закрытого ключа>

   * Используйте файл закрытого ключа и SSH для удаленного входа в систему. Пример:

        ssh –i <имя_файла_закрытого ключа> remoteuser@<hostname public ip>

     Кроме того, его можно использовать в клиенте как часть определения контекста вычислений Hadoop Spark для R Server. (См. дополнительные сведения об использовании Microsoft R Server в качестве клиента Hadoop в разделе [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Создание контекста вычислений для Spark) документа [Get started with ScaleR on Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Приступая к работе с ScaleR в Apache Spark).)

6. Щелкните **Источник данных**, чтобы выбрать источник данных в качестве первичного расположения файловой системы HDFS, используемой кластером. Выберите новую или существующую учетную запись хранения Azure или существующую учетную запись Data Lake Store.

   1. Чтобы указать учетную запись хранения Azure, щелкните **Выберите учетную запись хранения** и выберите имеющуюся учетную запись или создайте новую, щелкнув ссылку **Создать** в разделе **Выберите учетную запись хранения**.

      > [!NOTE]
      > При использовании ссылки **Создать** необходимо ввести имя новой учетной записи хранения. Если имя допустимо, появится зеленый флажок.

      **Контейнер по умолчанию** получит имя кластера по умолчанию. Оставьте это значение.

      Если вы выбрали создание учетной записи хранения, то щелкните **Расположение**, чтобы выбрать регион, в котором она будет создана.

      > [!IMPORTANT]
      > Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

   2. Если вы решили использовать существующее хранилище Data Lake Store, то выберите учетную запись хранения ADLS и добавьте в кластер удостоверение ADD, чтобы разрешить ему доступ к хранилищу.  Дополнительные сведения об этом процессе см. в статье [Создание кластера HDInsight с Data Lake Store с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

      Нажмите кнопку **Выбрать** , чтобы сохранить конфигурацию источника данных.

       ![Колонка "Источник данных"](./media/hdinsight-getting-started-with-r/datastore.png)

7. Выберите **Ценовые категории узла** , чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Если вы считаете, что вам не требуется больший размер кластера, оставьте число рабочих узлов по умолчанию: `4`. Оценочная стоимость кластера отобразится в колонке.

   > [!NOTE]
   > Если требуется, то на портале можно изменить количество рабочих узлов в кластере ("Кластер" -> "Параметры" -> "Изменить масштаб кластера").  Таким образом, кластер можно перевести в состояние ожидания, когда он не используется, или увеличить его емкость для выполнения масштабных заданий.
   >
   > 

    При изменении размера кластера, граничного узла и числа узлов данных необходимо учитывать следующие факторы:  

   * Если объем данных большой, производительность распределенных анализов R Server в Spark пропорциональна количеству рабочих узлов.  

   * Производительность анализов R Server линейно зависит от размера анализируемых данных. Например:  

     * Если объем данных небольшой или средний, для оптимальной производительности анализ данных необходимо выполнять в рамках локального контекста вычислений на граничном узле.  Дополнительные сведения об использовании локального контекста вычислений и контекста вычислений Spark см. в статье "Параметры контекста вычислений для R Server в HDInsight (предварительная версия)".<br>
     * Если войти на граничный узел и запустить там сценарий R, то все RX-функции, кроме RX-функций ScaleR, будут выполняться на нем <strong>локально</strong>. Поэтому необходимо соответствующим образом задать объем памяти и количество ядер граничного узла. Эти же рекомендации применимы при использовании на ноутбуке сервера R Server в HDI в качестве удаленного контекста вычислений.

     ![Колонка "Ценовые категории узла"](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Нажмите кнопку **Выбрать** , чтобы сохранить конфигурацию цен узла.

8. Выберите используемую **группу ресурсов**. Это может быть существующая группа ресурсов, или вы можете создать новую. Если вы щелкнули **Создать**, введите имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

   ![Колонка "Ценовые категории узла"](./media/hdinsight-getting-started-with-r/useexistingrg.png)

   

9. После просмотра выбранных параметров можно приступить к созданию кластера. Для этого выберите **Закрепить на начальной панели** и нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка.

   Вы также обратите внимание на ссылку на **Параметры автоматизации**. Если щелкнуть ее, будут отображены сценарии, которые можно использовать для автоматизации создания кластера с выбранной конфигурацией. Эти сценарии также доступны в записи кластера на портале Azure после его создания.

   | При создании                           | Создание завершено                        |
   | ---------------------------------------- | ---------------------------------------- |
   | ![Индикатор подготовки на начальной панели](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Плитка создания кластера](./media/hdinsight-getting-started-with-r/provisioned.png) |

   > [!NOTE]
   > Обычно создание кластера занимает около 20 минут. Вы можете отслеживать процесс создания с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
   >
   > 

## <a name="connect-to-rstudio-server"></a>Подключение к RStudio Server

Если вы решили включить в установку выпуск RStudio Server Community, вы сможете войти в RStudio двумя способами.

1. Вы можете перейти по следующему URL-адресу (где **имя_кластера** — это имя созданного кластера): 

    https://**имя_кластера**.azurehdinsight.net/rstudio/.

2. Или можно открыть запись для кластера на портале Azure, перейти по ссылке на панели мониторинга R Server и выбрать панель мониторинга R Studio:

     ![Доступ к панели мониторинга R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard.png)

   > [!IMPORTANT]
   > В любом случае при первом входе вам нужно будет пройти проверку подлинности два раза.  При первой проверке подлинности укажите имя пользователя и пароль администратора кластера. Во второй раз укажите имя пользователя и пароль SSH. При последующих входах вам нужно будет указывать только имя пользователя и пароль SSH. 

## <a name="connect-to-the-r-server-edge-node"></a>Подключение к пограничному узлу R Server
Подключиться к пограничному узлу R Server кластера HDInsight можно с помощью SSH.

    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

> [!NOTE]
> Адрес `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` также можно найти на портале Azure, выбрав кластер и последовательно щелкнув **Все параметры**, **Приложения**, **RServer**. Так можно отобразить информацию о конечной точке SSH для пограничного узла.
>
> ![Изображение конечной точки SSH для пограничного узла](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
> 

Если для защиты учетной записи SSH используется пароль, будет предложено ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i` , чтобы указать соответствующий закрытый ключ. Например, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Дополнительная информация об использовании SSH с HDInsight на основе Linux приведена в следующих статьях:

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

После установления подключения отобразится окно запроса, как показано ниже.

    username@ed00-myrser:~$

## <a name="use-the-r-console"></a>Использование консоли R
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

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Использование R Server в HDI из удаленного экземпляра Microsoft R Server или клиента Microsoft R Client
Для доступа к кластеру можно использовать пару открытого и закрытого ключей, как описано в одном из предыдущих разделов. Кроме того, доступ к контексту вычислений Hadoop Spark в HDI можно установить из удаленного экземпляра Microsoft R Server или Microsoft R Client, запущенного на настольном компьютере или ноутбуке. (Дополнительные сведения см. в подразделе об использовании Microsoft R Server в качестве клиента Hadoop в разделе, посвященном [созданию контекста вычислений для Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) [руководства по началу работы с функцией RevoScaleR в Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).)  Для этого при определении контекста вычислений RxSpark на ноутбуке необходимо указать следующие параметры: dfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches и sshProfileScript. Например:

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



## <a name="use-a-compute-context"></a>Использование контекста вычислений
Контекст вычислений позволяет определить, где будут выполнены вычисления — на локальном компьютере или пограничном узле, или же они будут распределены между узлами в кластере HDInsight.

1. В RStudio Server или консоли R (в сеансе SSH) выполните следующую команду, чтобы загрузить пример данных в хранилище по умолчанию для HDInsight.

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

   > [!NOTE]
   > Кроме того, для распределения вычислений между узлами кластера можно использовать MapReduce. Дополнительные сведения о контексте вычислений см. в статье с описанием [параметров контекста вычислений для R Server в HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).
   >
   > 

## <a name="distribute-r-code-to-multiple-nodes"></a>Распространение кода R на несколько узлов
Используя R Server, вы можете с легкостью взять имеющийся код R и запустить его на нескольких узлах кластера с помощью `rxExec`. Это удобно при очистке параметров или моделировании. Ниже приведен пример использования `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Если вы все еще используете контекст Spark или MapReduce, то этот метод вернет значение nodename рабочих узлов, на которых запущен код (`(Sys.info()["nodename"])`). Например, для кластера с четырьмя узлами результат будет аналогичен приведенному ниже.

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

## <a name="accessing-data-in-hive-and-parquet"></a>Доступ к данным в Hive и Parquet
Новая возможность, реализованная в версии R Server 9.0 и выше, обеспечивает прямой доступ к данным в Hive и Parquet для использования функций ScaleR в контексте вычислений Spark. Эти возможности, доступные в рамках новых функций источника данных ScaleR, называются RxHiveData и RxParquetData. Они используются со Spark SQL для загрузки данных непосредственно в Spark DataFrame и последующего анализа с помощью ScaleR.  

Ниже представлен пример кода с использованием новых функций. 



```
#..create a Spark compute context

myHadoopCluster <- rxSparkConnect(reset = TRUE)
```


```
#..retrieve some sample data from Hive and run a model 

hiveData <- RxHiveData("select * from hivesampletable", 
                 colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)
```


```
#..retrieve some sample data from Parquet and run a model 

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
```


``` 
#..check on Spark data objects, cleanup, and close the Spark session 

lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Дополнительные сведения об использовании этих новых функций см. в справочной документации по R Server (доступна с помощью команд ?RxHivedata и ?RxParquetData).  


## <a name="install-r-packages"></a>Установка пакетов R
Если вы хотите установить дополнительные пакеты R на граничный узел, то можете использовать `install.packages()` непосредственно из консоли R при подключении к граничному узлу по протоколу SSH. Но если требуется установить пакеты R на рабочие узлы кластера, необходимо использовать действие сценария.

Действия сценария — это сценарии Bash, которые используются для изменения конфигурации кластера HDInsight или установки дополнительного программного обеспечения. В данном случае они используются для установки дополнительных пакетов R. Чтобы установить дополнительные пакеты с помощью действия сценария, выполните следующее.

> [!IMPORTANT]
> Использовать действия сценария для установки дополнительных пакетов R можно только после создания кластера. Их не следует использовать во время создания кластера, так как сценарию требуется полностью установленный и настроенный R Server.
>
> 

1. На [портале Azure](https://portal.azure.com)выберите свой R Server в кластере HDInsight.
2. В колонке **Параметры** выберите **Действия скрипта** и щелкните **Отправить новый**, чтобы отправить новое действие сценария.

    ![Изображение колонки "Действия скрипта"](./media/hdinsight-getting-started-with-r/newscriptaction.png)
3. В колонке **Отправка действия скрипта** введите следующие сведения.

   * **Имя**: понятное имя для идентификации этого скрипта.

   * **URI bash-скрипта**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Заголовок**: флажок должен быть **снят**.

   * **Рабочая роль**: флажок должен быть **установлен**.

   * **Пограничные узлы**: флажок должен быть **снят**.

   * **Zookeeper**: флажок должен быть **снят**.

   * **Параметры**: устанавливаемые пакеты R. Например, `bitops stringr arules`

   * **Сохранить этот скрипт…**: флажок должен быть **установлен**.  

     > [!NOTE]
     > 1. По умолчанию все пакеты R устанавливаются из моментального снимка репозитория Microsoft MRAN, согласованного с версией сервера R Server, которая была установлена.  Если вы хотите установить более новые версии пакетов, они могут оказаться несовместимыми. Но этого можно избежать, указав `useCRAN` в качестве первого элемента в списке пакетов, например: `useCRAN bitops, stringr, arules`.  
     > 2. Для некоторых пакетов R потребуются дополнительные системные библиотеки Linux. Для удобства мы предварительно установили зависимости, необходимые для 100 наиболее популярных пакетов R. Однако, если для устанавливаемых пакетов R требуются дополнительные библиотеки, то необходимо скачать основной сценарий, используемый в этой статье, и добавить шаги для установки системных библиотек. Затем необходимо передать измененный сценарий в общедоступный контейнер больших двоичных объектов в службе хранилище Azure и использовать этот измененный сценарий для установки пакетов.
     >    Дополнительные сведения см. в статье [Разработка действий сценариев с помощью HDInsight](hdinsight-hadoop-script-actions-linux.md).  
     >
     >

     ![Добавление действия сценария](./media/hdinsight-getting-started-with-r/scriptaction.png)
     
4. Нажмите кнопку **Создать**, чтобы выполнить скрипт. После завершения выполнения пакеты R будут доступны на всех рабочих узлах.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете, как создать кластер HDInsight, который включает R Server, а также основные принципы использования консоли R в сеансе SSH, узнайте другие способы работы с R Server в HDInsight в следующих статьях.

* [Установка RStudio с R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Варианты контекста вычислений для R Server в HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-storage.md)




<!--HONumber=Dec16_HO2-->



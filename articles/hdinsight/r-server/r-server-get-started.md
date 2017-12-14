---
title: "Приступая к работе с R Server в HDInsight в Azure | Документация Майкрософт"
description: "Узнайте, как создать Apache Spark в кластере HDInsight, который содержит R Server, и отправить скрипт R в кластер."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: f6e13a159cf7f5a1302749d74d2833cf1a035464
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="get-started-using-r-server-on-hdinsight"></a>Приступая к работе с R Server в HDInsight

HDInsight поддерживает интеграцию R Server в кластер HDInsight. Эта поддержка позволяет скриптам R использовать MapReduce и Spark для выполнения распределенных вычислений. В этом документе вы узнаете, как создать R Server в кластере HDInsight, а затем запустить сценарий R, демонстрирующий использование Spark для распределенных вычислений на R.


## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Дополнительные сведения см. в статье [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Как получить бесплатную пробную версию Azure для тестирования Hadoop в HDInsight).
* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Ключи SSH (необязательно)**. Для защиты учетной записи SSH, которая используется для подключения к кластеру, можно применять пароль или открытый ключ. Использовать пароль проще, он позволяет приступить к работе, не создавая пару открытого и закрытого ключей. Тем не менее использовать ключ безопаснее.

> [!NOTE]
> В инструкциях в этом документе предполагается, что вы используете пароль.


## <a name="automated-cluster-creation"></a>Автоматизированное создание кластера

Вы можете автоматизировать создание R Servers HDInsight с помощью шаблонов Azure Resource Manager, пакета SDK и PowerShell.

* Дополнительные сведения о создании R Server с помощью шаблона Azure Resource Management см. в статье [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Развертывание кластера R Server HDInsight).
* Сведения о создании R Server с помощью пакета SDK .NET см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Сведения о развертывании R Server с помощью PowerShell см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Создание кластера с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выберите **Создать** -> **Аналитика** -> **HDInsight**.

    ![Изображение, на котором показано создание нового кластера](./media/r-server-get-started/newcluster.png)

3. Выберите **Быстрое создание** и в поле **Имя кластера** введите имя кластера. Если у вас несколько подписок Azure, с помощью элемента **Подписка** выберите нужную.

    ![Выбор имени кластера и подписки](./media/r-server-get-started/clustername.png)

4. Выберите **Тип кластера**, чтобы открыть колонку **Настройка кластера**. В колонке **Конфигурация кластера** выберите следующие параметры.

    * **Тип кластера**: R Server.
    * **Версия**: выберите версию R Server для установки в кластере. В данный момент доступна версия ***R Server 9.1 (HDI 3.6)***. Заметки о выпуске для доступных версий R Server см. [здесь](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **Выпуск R Studio Community для R Server**: эта среда IDE на основе браузера устанавливается по умолчанию на граничном узле. Если вы не хотите устанавливать ее, просто снимите флажок. Если же вы решили установить среду, на портале в колонке приложения для созданного кластера вы найдете URL-адрес для входа в RStudio Server.
    * Для других параметров оставьте значения по умолчанию. Затем с помощью кнопки **Выбрать** сохраните тип кластера.

        ![Снимок экрана колонки "Тип кластера"](./media/r-server-get-started/clustertypeconfig.png)

5. Введите **имя пользователя** и **пароль** для входа в кластер.

    Укажите **имя пользователя SSH**. Протокол SSH используется для удаленного подключения к кластеру с помощью клиента **Secure Shell (SSH)**. Пользователя SSH можно указать в этом диалоговом окне или на вкладке "Конфигурация" после создания кластера. В качестве **имени пользователя SSH** для R Server необходимо указать значение remoteuser.  **Если используется другое имя, после создания кластера потребуется выполнить дополнительное действие.**

    Если вы не используете открытый ключ, установите флажок **Использовать тот же пароль в учетных данных кластера**, чтобы в качестве типа аутентификации использовать **пароль**.  Для доступа к R Server в кластере через удаленный клиент (например, RTVS, RStudio или другой классический интерфейс IDE) требуется пара открытого и закрытого ключей. При установке выпуска RStudio Server Community необходимо выбрать пароль SSH.     

    Чтобы создать и использовать пару открытого и закрытого ключей, снимите флажок **Использовать тот же пароль в учетных данных кластера**, а затем выберите **Открытый ключ** и выполните приведенные ниже действия. При выполнении этих указаний предполагается, что установлена среда Cygwin с программой ssh-keygen или эквивалентным средством.

    * Создайте пару открытого и закрытого ключей с помощью командной строки на ноутбуке:

        ssh-keygen -t rsa -b 2048

    * Следуйте указаниям, чтобы присвоить имя файлу ключа, а затем введите парольную фразу, чтобы повысить уровень безопасности. Экран должен выглядеть примерно так:

        ![Командная строка SSH в Windows](./media/r-server-get-started/sshcmdline.png)

    * Эта команда создает файл закрытого и открытого ключей с именем <имя_файла_закрытого_ключа>.pub (например, furiosa и furiosa.pub).

        ![Каталог SSH](./media/r-server-get-started/dir.png)

    * Затем при назначении учетных данных кластера HDI укажите файл открытого ключа (&#42;.pub) и, наконец, подтвердите группу ресурсов и регион, а затем нажмите кнопку **Далее**.

        ![Колонка учетных данных](./media/r-server-get-started/publickeyfile.png)  

   * Измените разрешения в файле закрытого ключа на ноутбуке:

        chmod 600 <имя_файла_закрытого ключа>

   * Используйте файл закрытого ключа и SSH для удаленного входа в систему:

        ssh –i <имя_файла_закрытого_ключа> remoteuser@<hostname public ip>.

      Кроме того, его можно использовать в клиенте как часть определения контекста вычислений Hadoop Spark для R Server. Дополнительные сведения см. в подразделе об **использовании Microsoft R Server в качестве клиента Hadoop** в разделе, посвященном [созданию контекста вычислений для Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark).

6. Выберите "Быстрое создание", чтобы перейти к колонке **Хранилище** и выбрать параметры учетной записи хранения, которая будет использоваться в качестве первичного расположения файловой системы HDFS, используемой кластером. Выберите новую или существующую учетную запись хранения Azure или существующую учетную запись Data Lake Store.

    - Если вы выбрали учетную запись хранения Azure, выберите существующую учетную запись хранения, щелкнув **Выбрать учетную запись хранения** и выбрав соответствующую учетную запись. Вы можете также создать учетную запись, используя ссылку **Создать** в разделе **Выберите учетную запись хранения**.

      > [!NOTE]
      > Нажав ссылку **Создать**, введите имя для новой учетной записи хранения. Если имя допустимо, то отобразится зеленый флажок.

      **Контейнер по умолчанию** получит имя кластера по умолчанию. Оставьте значение по умолчанию.

      Если вы выбрали создание учетной записи хранения, вам будет предложено выбрать **расположение**, то есть регион, в котором она будет создана.  

         ![Колонка "Источник данных"](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > Выбранное расположение для источника данных по умолчанию будет определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

    - Если вы решили использовать существующее хранилище Data Lake Store, выберите учетную запись хранения ADLS и добавьте в кластер удостоверение *ADD*, чтобы разрешить ему доступ к хранилищу. Дополнительные сведения об этом процессе см. в статье [Создание кластера HDInsight с хранилищем Data Lake Store с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию источника данных.


7. В колонке **Сводка** отобразятся все параметры, которые можно проверить. Здесь можно будет изменить **размер кластера**, количество серверов в кластере, а также указать **действия сценария**, которые нужно выполнить. Если вы считаете, что вам не требуется больший размер кластера, оставьте число рабочих узлов по умолчанию: `4`. Оценочная стоимость кластера отображается в колонке.

    ![Сводка по кластерам](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > При необходимости на портале можно изменить число рабочих узлов в кластере (**Кластер** -> **Параметры** -> **Изменить масштаб кластера**).  Изменив размер, кластер можно перевести в состояние ожидания, когда он не используется, или увеличить его емкость для выполнения масштабных заданий.
   >
   >

   При изменении размера кластера, граничного узла и числа узлов данных необходимо учитывать следующие факторы:  

   * Если объем данных большой, производительность распределенных анализов R Server в Spark пропорциональна количеству рабочих узлов.  

   * Производительность анализов R Server линейно зависит от размера анализируемых данных. Например:  

     * Если объем данных небольшой или средний, для оптимальной производительности анализ данных необходимо выполнять в рамках локального контекста вычислений на граничном узле.  Дополнительные сведения об использовании локального контекста вычислений и контекста вычислений Spark см. в статье "Варианты контекста вычислений для R Server в HDInsight".<br>
     * Если войти на граничный узел и запустить там сценарий R, то все RX-функции, кроме RX-функций ScaleR, будут выполняться на нем <strong>локально</strong>. Поэтому необходимо соответствующим образом задать объем памяти и количество ядер граничного узла. Эти же рекомендации применимы при использовании на ноутбуке сервера R Server в HDI в качестве удаленного контекста вычислений.

     ![Колонка "Ценовые категории узла"](./media/r-server-get-started/pricingtier.png)

     Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию цен узла.

   Обратите внимание на ссылку для **скачивания шаблона и параметров**. Щелкните ее, чтобы отобразить сценарии, которые можно использовать для автоматизации создания кластера с выбранной конфигурацией. Эти скрипты также доступны в записи кластера на портале Azure после создания кластера.

   > [!NOTE]
   > Обычно создание кластера занимает около 20 минут. Вы можете отслеживать процесс создания с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Подключение к RStudio Server

Если вы решили включить в установку выпуск RStudio Server Community, вы сможете войти в RStudio двумя способами.

1. Перейдите по следующему URL-адресу (где **имя_кластера** — это имя созданного кластера):

    https://**имя_кластера**.azurehdinsight.net/rstudio/.

2. Откройте запись для кластера на портале Azure, перейдите по ссылке к **панелям мониторинга R Server** и выберите **панель мониторинга R Studio**:

     ![Доступ к панели мониторинга R Studio](./media/r-server-get-started/rstudiodashboard1.png)

     ![Доступ к панели мониторинга R Studio](./media/r-server-get-started/rstudiodashboard2.png)

   > [!IMPORTANT]
   > В любом случае при первом входе понадобится выполнить аутентификацию дважды.  В первый раз укажите *имя пользователя* и *пароль* администратора кластера. Во второй раз укажите *идентификатор пользователя* и *пароль* SSH. Для следующих операций входа вам нужно будет указывать только *идентификатор пользователя* и *пароль SSH*.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Подключение к пограничному узлу R Server

К пограничному узлу R Server кластера HDInsight можно подключиться с помощью команды в SSH:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Адрес `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` можно найти на портале Azure, выбрав кластер и последовательно щелкнув **Все параметры** -> **Приложения** -> **R Server**. Так можно отобразить информацию о конечной точке SSH для пограничного узла.
>
> ![Изображение конечной точки SSH для пограничного узла](./media/r-server-get-started/sshendpoint.png)
>
>

Если для защиты учетной записи SSH используется пароль, то предлагается ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i`, чтобы указать соответствующий закрытый ключ. Например:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Дополнительные сведения см. в руководстве по [подключению к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

После установления подключения отобразится окно запроса, как показано ниже.

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Включение нескольких параллельных подключений пользователей

Можно создать несколько параллельных подключений пользователей, добавив пользователей для граничного узла, на котором выполняется версия RStudio Community.

При создании кластера HDInsight необходимо указать двух пользователей: для HTTP и SSH.

![Параллельное подключение пользователя 1](./media/r-server-get-started/concurrent-users-1.png)

- **Имя пользователя для входа в кластер** — пользователь HTTP для проверки подлинности с помощью шлюза HDInsight, который применяется для защиты созданных вами кластеров HDInsight. Этот пользователь HTTP служит для доступа к пользовательскому интерфейсу Ambari, пользовательскому интерфейсу YARN, а также другим компонентам пользовательского интерфейса.
- **Имя пользователя Secure Shell (SSH)** — пользователь SSH для доступа к кластеру через безопасную оболочку. Этот пользователь применяется в системе Linux для всех головных, рабочих и граничных узлов. Таким образом, с помощью безопасной оболочки вы можете получить доступ к любому узлу в удаленном кластере.

Для входа в версию R Studio Server Community, используемую в Microsoft R Server в кластере HDInsight, принимаются только имя пользователя и пароль Linux. Эта версия не поддерживает передачу токенов. Поэтому, если вы создали новый кластер и хотите использовать R Studio для доступа к нему, вам потребуется выполнить вход дважды.

- Сначала вы войдете с помощью учетных данных пользователя HTTP через шлюз HDInsight: 

    ![Параллельное подключение пользователя 2а](./media/r-server-get-started/concurrent-users-2a.png)

- Затем используйте учетные данные SSH для входа в RStudio:
  
    ![Параллельное подключение пользователя 2б](./media/r-server-get-started/concurrent-users-2b.png)

Сейчас при подготовке кластера HDInsight можно создать только одну учетную запись пользователя SSH. Поэтому, чтобы предоставить нескольким пользователям доступ к Microsoft R Server в кластерах HDInsight, необходимо создать дополнительных пользователей в системе Linux.

Так как версия RStudio Server Community работает на граничном узле кластера, нужно выполнить несколько шагов:

1. с помощью созданного пользователя SSH войти на граничный узел;
2. добавить нескольких пользователей Linux на граничный узел;
3. использовать версию RStudio Community с помощью созданного пользователя.

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Шаг 1. Вход на граничный узел с помощью созданного пользователя SSH

Скачайте любое средство SSH (например, Putty) и с помощью существующего пользователя SSH выполните вход. Чтобы войти на граничный узел, следуйте инструкциям по [подключению к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Адрес граничного узла для R Server в кластере HDInsight выгляди так: *имя_кластера-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>Шаг 2. Добавление дополнительных пользователей Linux на граничный узел

Чтобы добавить пользователя на граничный узел, выполните эти команды:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Вы должны получить следующий результат: 

![Параллельное подключение пользователя 3](./media/r-server-get-started/concurrent-users-3.png)

При появлении запроса на ввод текущего пароля Kerberos нажмите клавишу **ВВОД**, чтобы игнорировать его. Параметр `-m` в команде `useradd` указывает, что система создаст домашнюю папку пользователя, которая обязательна для версии RStudio Community.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Шаг 3. Использование версии RStudio Community с помощью созданного пользователя

Войдите в RStudio с помощью учетных данных созданного пользователя:

![Параллельное подключение пользователя 4](./media/r-server-get-started/concurrent-users-4.png)

Обратите внимание, что в RStudio обозначено, что вы вошли в кластер с помощью нового пользователя (в данном случае это пользователь *sshuser6*). 

![Параллельное подключение пользователя 5](./media/r-server-get-started/concurrent-users-5.png)

Вы также можете войти параллельно с помощью исходных учетных данных (по умолчанию — *sshuser*) в другом окне браузера.

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


Обратите внимание, что переданные задания отображаются под другим именем пользователя в пользовательском интерфейсе YARN:

![Параллельное подключение пользователя 6](./media/r-server-get-started/concurrent-users-6.png)

Обратите внимание, что у добавленных пользователей нет привилегированных прав в системе Linux, но у них есть тот же доступ ко всем файлам в удаленном хранилище HDFS и WASB.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Использование консоли R

1. В сеансе SSH используйте следующую команду, чтобы запустить консоль R:  

        R

2. Вы должны увидеть результат, аналогичный приведенному ниже:
    
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

3. В строке ввода `>` можно ввести код R. R Server содержит пакеты, которые позволяют легко взаимодействовать с Hadoop и выполнять распределенные вычисления. Например, используйте следующую команду, чтобы просмотреть корень файловой системы по умолчанию для кластера HDInsight.

        rxHadoopListFiles("/")

4. Также можно использовать WASB-адресацию.

        rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Использование R Server в HDI из удаленного экземпляра Microsoft R Server или клиента Microsoft R Client

Доступ к контексту вычислений Hadoop Spark в HDI можно установить из удаленного экземпляра Microsoft R Server или Microsoft R Client, запущенного на персональном компьютере или ноутбуке. Дополнительные сведения см. в подразделе об **использовании Microsoft R Server в качестве клиента Hadoop** в разделе, посвященном [созданию контекста вычислений для Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md). Для этого при определении контекста вычислений RxSpark на ноутбуке необходимо указать следующие параметры: dfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches и sshProfileScript. Например:


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

Контекст вычислений позволяет определить, где будут выполнены вычисления — на локальном компьютере или пограничном узле, или же они будут распределены между узлами в кластере HDInsight.

1. В RStudio Server или консоли R (в сеансе SSH) выполните следующий код, чтобы загрузить пример данных в хранилище по умолчанию для HDInsight:

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
   > Кроме того, для распределения вычислений между узлами кластера можно использовать MapReduce. Дополнительные сведения о контексте вычислений см. в статье с описанием [параметров контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Распространение кода R на несколько узлов

Используя R Server, вы можете с легкостью взять имеющийся код R и запустить его на нескольких узлах кластера с помощью `rxExec`. Эта функция удобна при очистке параметров или моделировании. Следующий код является примером использования `rxExec`.

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


## <a name="accessing-data-in-hive-and-parquet"></a>Доступ к данным в Hive и Parquet

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


Дополнительные сведения об использовании этих новых функций см. в справочной документации по R Server (доступна с помощью команд `?RxHivedata` и `?RxParquetData`).  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Установка дополнительных пакетов R на граничный узел

Если вы хотите установить дополнительные пакеты R на граничный узел, то можете использовать `install.packages()` непосредственно из консоли R при подключении к граничному узлу по протоколу SSH. Но если требуется установить пакеты R на рабочие узлы кластера, необходимо использовать действие сценария.

Действия скриптов — это скрипты Bash, которые используются для изменения конфигурации кластера HDInsight или установки дополнительного программного обеспечения, например дополнительных пакетов R. Чтобы установить дополнительные пакеты с помощью действия скрипта, выполните следующее:

> [!IMPORTANT]
> Использовать действия сценария для установки дополнительных пакетов R можно только после создания кластера. Не используйте процедуру во время создания кластера, так как скрипту требуется полностью установленный и настроенный R Server.
>
>

1. На [портале Azure](https://portal.azure.com)выберите свой R Server в кластере HDInsight.

2. В колонке **Параметры** выберите **Действия скрипта** и щелкните **Отправить новый**, чтобы отправить новое действие скрипта.

   ![Изображение колонки "Действия скрипта"](./media/r-server-get-started/scriptaction.png)

3. В колонке **Отправка действия скрипта** введите следующие сведения:

   * **Имя**: понятное имя для идентификации этого скрипта.

   * **URI bash-скрипта**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Заголовок**: флажок для этого элемента должен быть **снят**.

   * **Рабочая роль**: флажок для этого элемента должен быть **установлен**.

   * **Пограничные узлы**: флажок для этого элемента должен быть **снят**.

   * **Zookeeper**: флажок для этого элемента должен быть **снят**.

   * **Параметры**: устанавливаемые пакеты R. Например, `bitops stringr arules`

   * **Сохранить этот скрипт…**: флажок для этого элемента должен быть **установлен**.  

   > [!NOTE]
   > 1. По умолчанию все пакеты R устанавливаются из моментального снимка репозитория Microsoft MRAN, согласованного с версией сервера R Server, которая была установлена. Если вы хотите установить более новые версии пакетов, существует риск несовместимости. Но эту установку можно выполнить, указав `useCRAN` в качестве первого элемента список пакетов, например `useCRAN bitops, stringr, arules`.  
   > 2. Для некоторых пакетов R требуются дополнительные системные библиотеки Linux. Для удобства мы предварительно установили зависимости, необходимые для 100 наиболее популярных пакетов R. Однако если для устанавливаемых пакетов R требуются дополнительные библиотеки, то необходимо скачать основной скрипт, используемый в этой статье, и добавить шаги для установки системных библиотек. Затем необходимо передать измененный сценарий в общедоступный контейнер больших двоичных объектов в службе хранилище Azure и использовать этот измененный сценарий для установки пакетов.
   >    Дополнительные сведения см. в статье [Разработка действий сценариев с помощью HDInsight](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Добавление действия сценария](./media/r-server-get-started/submitscriptaction.png)

4. Нажмите кнопку **Создать**, чтобы выполнить скрипт. После завершения выполнения пакеты R будут доступны на всех рабочих узлах.


## <a name="using-microsoft-r-server-operationalization"></a>Ввод в эксплуатацию для Microsoft R Server

Когда вы завершите моделирование данных, готовую модель можно ввести в эксплуатацию для составления прогнозов. Чтобы настроить ввод в эксплуатацию на сервере Microsoft R Server, сделайте следующее.

Сначала подключитесь к граничному узлу по протоколу SSH. Например, 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Открыв сеанс SSH, перейдите в соответствующую версию каталога и запустите DLL-файл dotnet с помощью команды sudo. 

- Для Microsoft R Server 9.1:

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0 sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Для Microsoft R Server 9.0:

    cd /usr/lib64/microsoft-deployr/9.0.1 sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Чтобы настроить ввод в эксплуатацию для Microsoft R Server в универсальной конфигурации, выполните следующую процедуру:

1. Выберите Configure R Server for Operationalization (Настройка R Server для ввода в эксплуатацию).
2. Выберите "A. One-box (web + compute nodes)" (A. Универсальная (веб + вычислительные узлы)).
3. Введите пароль для пользователя с правами **администратора**.

![Универсальная конфигурация](./media/r-server-get-started/admin-util-one-box-.png)

В качестве дополнительного шага можно запустить диагностический тест, как показано ниже.

1. Выберите "6. Run diagnostic tests" (6. Запуск диагностических тестов).
2. Выберите "A. Test configuration" (А. Проверка конфигурации).
3. Введите имя пользователя admin и пароль, настроенный на предыдущем шаге.
4. Убедитесь, что общая проверка работоспособности проходит успешно (Overall Health = pass).
5. Выйдите из служебной программы администрирования.
6. Завершите сеанс SSH.

![Диагностика перед вводом в эксплуатацию](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>**Длительные задержки при использовании веб-службы на Spark**
>
>Если при попытке использовать веб-службы, созданные с использованием функций mrsdeploy, в вычисленном контексте Spark возникают значительные задержки, может потребоваться добавить некоторые отсутствующие папки. Приложение Spark принадлежит пользователю с именем *rserve2* при вызове из веб-службы с помощью функций mrsdeploy. Для обхода этой проблемы:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Мы завершили настройку для ввода в эксплуатацию. Теперь с помощью пакета mrsdeploy вы можете подключить RClient к средству операционализации на граничном узле и использовать разные функции, например [удаленное выполнение](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) и [веб-службы](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). В зависимости от того, настроен ли кластер в виртуальной сети, может потребоваться настроить туннелирование с перенаправлением портов через сеанс SSH. В следующих разделах описано, как настроить этот туннель.

### <a name="rserver-cluster-on-virtual-network"></a>Кластер R Server в виртуальной сети

Убедитесь, что трафик через порт 12800 направляется на граничный узел. Это позволит использовать граничный узел для подключения к средству операционализации.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Если `remoteLogin()` не может подключиться к граничному узлу, но вам удается подключиться к нему по протоколу SSH, проверьте наличие и правильность настройки правила, разрешающего трафик через порт 12800. Если и после этого проблема не исчезнет, в качестве решения можно настроить туннелирование с перенаправлением портов через SSH. Инструкции см. в следующем разделе.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Кластер RServer не настроен в виртуальной сети

Если ваш кластер не настроен в виртуальной сети или с подключением через виртуальную сеть возникают проблемы, можно использовать туннелирование с перенаправлением портов через SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Также это можно настроить с помощью программы Putty.

![Подключение SSH через Putty](./media/r-server-get-started/putty.png)

Теперь при открытии сеанса SSH трафик от порта 12800 на вашем компьютере будет передаваться на порт 12800 граничного узла через сеанс SSH. Убедитесь, что вы используете `127.0.0.1:12800` в методе `remoteLogin()`. Это позволит войти в средство операционализации на граничном узле при использовании перенаправления портов.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Как масштабировать вычислительные узлы системы ввода в эксплуатацию Microsoft R Server на рабочих узлах HDInsight?

### <a name="decommission-the-worker-nodes"></a>Вывод рабочих узлов из эксплуатации

Управление Microsoft R Server через Yarn сейчас не поддерживается. Если вы не выведете из эксплуатации рабочие узлы, диспетчер ресурсов YARN не сможет правильно работать, так как не получит информацию об использовании ресурсов сервером. Чтобы этого избежать, мы рекомендуем вывести из эксплуатации рабочие узлы, прежде чем масштабировать вычислительные узлы.

Действия для вывода рабочих узлов из эксплуатации:

* Войдите в консоль Ambari для вашего кластера HDI и выберите вкладку Hosts (Узлы).
* Выберите рабочие узлы (которые нужно вывести из эксплуатации), выберите Actions > Selected Hosts > Hosts (Действия > Выбранные узлы > Узлы), а затем щелкните Turn ON Maintenance Mode (Включить режим обслуживания). Например, на следующем рисунке для вывода из эксплуатации выбраны узлы wn3 и wn4.  

   ![Вывод рабочих узлов из эксплуатации](./media/r-server-get-started/get-started-operationalization.png)  

* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) и щелкните **Stop All Components** (Остановить все компоненты).
* Отмените выбор рабочих узлов и выберите головные узлы.
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) > **Restart All Components** (Перезапустить все компоненты).

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Настройка вычислительных узлов на каждом из рабочих узлов, выведенных из эксплуатации

1. Поочередно откройте сеанс SSH для каждого рабочего узла, выведенного из эксплуатации.
2. Запустите служебную программу администрирования с помощью `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Введите "1", чтобы выбрать параметр Configure R Server for Operationalization (Настройка R Server для ввода в эксплуатацию).
4. Введите "c", чтобы выбрать пункт "C. Compute node" (C. Вычислительный узел). Это настраивает вычислительный узел на рабочем узле.
5. Выйдите из служебной программы администрирования.

### <a name="add-compute-nodes-details-on-web-node"></a>Добавление сведений о вычислительных узлах на веб-узел

Когда вы завершите настройку всех рабочих узлов, выведенных из эксплуатации, чтобы они выполняли функции вычислительных узлов, вернитесь на граничный узел и добавьте в конфигурацию веб-узла Microsoft R Server IP-адреса рабочих узлов, выведенных из эксплуатации.

* Подключитесь к граничному узлу по протоколу SSH.
* Запустите `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Найдите раздел URI и добавьте в него информацию об IP-адресах и портах рабочих узлов.

    ![Командная строка для выведенных из эксплуатации рабочих узлов](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Дальнейшие действия

Теперь необходимо понять, как создать новый кластер HDInsight, который включает в себя R Server и основные сведения об использовании консоли R в сеансе SSH. В следующих разделах описаны другие способы работы с R Server в HDInsight и его администрирования:

* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)](r-server-storage.md)

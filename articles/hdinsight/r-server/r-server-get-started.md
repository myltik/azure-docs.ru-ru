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
ms.openlocfilehash: e688068efb41cdccbeb23de3c8ad7a09021e5b3f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Начало работы с R Server в HDInsight

Azure HDInsight поддерживает интеграцию R Server в кластер HDInsight. Эта поддержка позволяет скриптам R использовать MapReduce и Spark для выполнения распределенных вычислений. Из этой статьи вы узнаете, как создать Microsoft R Server в кластере HDInsight. Также вы узнаете, как запустить скрипт R с примерами использования Spark для распределенных вычислений R.


## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Этот процесс описан в видеоролике [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Как получить бесплатную пробную версию Azure для тестирования Hadoop в HDInsight).
* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Ключи SSH (необязательно)**. Для защиты учетной записи SSH, используемой для подключения к кластеру, можно применять пароль или открытый ключ. Проще использовать пароль, чтобы сразу приступить к работе, не создавая пару из открытого и закрытого ключей. Тем не менее использовать ключ безопаснее.

  > [!NOTE]
  > В инструкциях этой статьи предполагается, что вы используете пароль.


## <a name="automate-cluster-creation"></a>Автоматизация создания кластера

Вы можете автоматизировать создание R Server в HDInsight с помощью шаблонов Azure Resource Manager, пакета SDK или PowerShell.

* Создание экземпляра сервера R с помощью шаблона Azure Resource Manager см. в [описании шаблона кластера HDInsight для R Server](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Сведения о создании экземпляра R Server с помощью пакета SDK .NET см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Развертывание сервера R с помощью PowerShell описано в статье [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Создание кластера с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **Создать** > **Аналитика** > **HDInsight**.

    ![Изображение, на котором показано создание нового кластера](./media/r-server-get-started/newcluster.png)

3. Выберите **Быстрое создание** и введите имя кластера в области **Имя кластера**. Если у вас несколько подписок Azure, выберите нужную в поле **Подписка**.

    ![Выбор имени кластера и подписки](./media/r-server-get-started/clustername.png)

4. Выберите **Тип кластера**, чтобы открыть панель **Настройка кластера**. На панели **Конфигурация кластера** выберите следующие параметры:

    * Для параметра **Тип кластера** выберите **R Server**.
    * **Версия**: выберите версию R Server для установки в кластере. В данный момент доступна версия **R Server 9.1 (HDI 3.6)**. Заметки о выпуске для доступных версий R Server см. [на сайте MSDN](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **Выпуск R Studio Community для R Server**: эта среда IDE с браузерным интерфейсом устанавливается по умолчанию на граничном узле. Если вы не хотите ее устанавливать, снимите соответствующий флажок. Если же вы решили установить среду, после ее создания вы найдете URL-адрес для входа в RStudio Server на портале, в колонке приложений для созданного кластера.
    * Для других параметров оставьте значения по умолчанию. Затем с помощью кнопки **Выбрать** сохраните тип кластера.

        ![Снимок панели "Тип кластера"](./media/r-server-get-started/clustertypeconfig.png)

5. На панели **Основные сведения** введите имя пользователя и пароль для кластера в полях **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**.

6. В области **Имя пользователя Secure Shell (SSH)** укажите имя пользователя для входа через SSH. Протокол SSH используется для удаленного подключения к кластеру с помощью клиента SSH. Пользователя SSH можно указать в этой же области или уже после установки кластера на вкладке **Конфигурация**.
   
   > [!NOTE] 
   > По умолчанию R Server ожидает значение remoteuser в качестве имени пользователя SSH. Если вы хотите использовать другое имя, его нужно будет настроить отдельно после создания кластера.

7. Если вы не используете открытый ключ, сохраните флажок в поле **Использовать тот же пароль в учетных данных кластера**, чтобы в качестве типа аутентификации использовать **пароль**. Для доступа к R Server в кластере через удаленный клиент (например, Инструменты R для Visual Studio, RStudio или другой классический интерфейс IDE) требуется пара из открытого и закрытого ключей. При установке выпуска RStudio Server Community необходимо выбрать пароль SSH.     

   Чтобы создать и использовать пару из открытого и закрытого ключей, снимите флажок **Использовать тот же пароль в учетных данных кластера**. Также выберите вариант **Открытый ключ** и выполните следующие действия. При выполнении этих указаний предполагается, что установлена среда Cygwin с программой ssh-keygen или эквивалентным средством.

   a. Создайте пару открытого и закрытого ключей с помощью командной строки на ноутбуке:

        ssh-keygen -t rsa -b 2048

   Б. Следуйте указаниям, чтобы присвоить имя файлу ключа, а затем введите парольную фразу, чтобы повысить уровень безопасности. Экран должен выглядеть примерно так:

      ![Окно командной строки SSH в Windows](./media/r-server-get-started/sshcmdline.png)

      Эта команда создает файлы закрытого и открытого ключей с одинаковыми именами и расширением .pub для открытого ключа. В нашем примере это файлы с именами furiosa и furiosa.pub.

      ![Пример выходных данных команды dir](./media/r-server-get-started/dir.png)

   c. При вводе учетных данных кластера HDI укажите файл открытого ключа (PUB-файл). Проверьте значения для группы ресурсов и региона, а затем выберите **Далее**.

      ![Панель учетных данных](./media/r-server-get-started/publickeyfile.png)  

   d. Измените разрешения для файла закрытого ключа на локальном компьютере.

        chmod 600 <private-key-filename>

   д. Используйте файл закрытого ключа и SSH для удаленного входа в систему:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Кроме того, файл закрытого ключа можно использовать в клиенте как часть определения контекста вычислений Hadoop Spark для R Server. Дополнительные сведения см. в статье [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Создание контекста вычислений для Spark).

8. В процессе быстрого создания откроется панель **Служба хранилища**. Здесь следует выбрать параметры учетной записи хранения, которая будет использоваться в качестве основного расположения файловой системы HDFS для кластера. Выберите новую или существующую учетную запись хранения Azure или существующую учетную запись Azure Data Lake Store.

    - При этом можно выбрать существующую учетную запись хранения с помощью элемента **Выберите учетную запись хранения**. Вы можете также создать учетную запись с помощью ссылки **Создать** в разделе **Выбор учетной записи хранения**.

      > [!NOTE]
      > При использовании ссылки **Создать** необходимо ввести имя новой учетной записи хранения. Если имя допустимо, то отобразится зеленый флажок.

      **Контейнер по умолчанию** получает такое же имя, как и кластер. Оставьте значение по умолчанию.

      Если вы выбрали новую учетную запись хранилища, выберите регион для нее в параметре командной строки **Location**.  

         ![Панель источника данных](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > Выбранное расположение для источника данных по умолчанию будет определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

    - Если вы хотите использовать существующую учетную запись Azure Data Lake Store, выберите нужную. Затем добавьте в кластер удостоверение *ADD*, чтобы разрешить доступ к хранилищу. Дополнительные сведения об этом процессе см. в статье [Create HDInsight clusters with Data Lake Store by using the Azure portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) (Создание кластеров HDInsight в Data Lake Store с помощью портала Azure).

    Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию источника данных.


9. Откроется панель **Сводка**, на которой вы можете проверить все параметры. Здесь можно изменить размер кластера, то есть число серверов в кластере. Также вы можете указать скрипты, которые нужно выполнять. Если вы не уверены, что вам нужен больший размер кластера, сохраните здесь значение по умолчанию: **4** рабочих узла. Также эта панель демонстрирует предполагаемую стоимость кластера.

    ![Сводная информация о кластере](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > При необходимости на портале можно изменить число рабочих узлов кластера (**Кластер** > **Параметры** > **Изменить масштаб кластера**). Это удобно, когда нужно уменьшить размер кластера, когда он не используется, или увеличить производительность для масштабных заданий.
   >
   >

   При изменении размера кластера, граничного узла и числа узлов данных необходимо учитывать следующие рекомендации:  

   * Если объем данных большой, производительность распределенных анализов R Server в Spark пропорциональна количеству рабочих узлов.  

   * Производительность анализов R Server линейно зависит от размера анализируемых данных. Например:   

     * При малом или среднем объеме данных оптимальная производительность достигается при анализе данных на граничном узле в локальном контексте вычислений. Дополнительные сведения об использовании локального контекста вычислений и контекста вычислений Spark см. в статье [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md).<br>
     * Если вы войдете на граничный узел и запустите там сценарий R, все RX-функции, кроме ScaleR, будут выполняться на нем *локально*. Поэтому важно правильно задать объем памяти и число ядер граничного узла. Эти же рекомендации применимы при использовании на ноутбуке сервера R Server в HDI в качестве удаленного контекста вычислений.

   Нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию цен узла.

   ![Панель ценовых категорий узлов](./media/r-server-get-started/pricingtier.png)

   Обратите внимание на ссылку для **скачивания шаблона и параметров**. Щелкните эту ссылку, чтобы отобразить скрипты, которые можно использовать для автоматизации создания кластера с выбранной конфигурацией. Эти же скрипты станут доступны в записи кластера на портале Azure после его создания.

   > [!NOTE]
   > Обычно создание кластера занимает около 20 минут. Вы можете контролировать процесс создания с помощью элемента на начальной панели или элемента **Уведомления** в левой части страницы.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Подключение к RStudio Server

Если вы включили в установку выпуск RStudio Server Community, вы сможете войти в RStudio двумя способами:

- Откройте в браузере следующий URL-адрес (где *имя_кластера* — это имя созданного кластера):

    https://*имя_кластера*.azurehdinsight.net/rstudio/.

- Откройте запись для кластера на портале Azure, перейдите по ссылке к **панелям мониторинга R Server** и выберите **панель мониторинга R Studio**.

  ![Доступ к панели мониторинга RStudio](./media/r-server-get-started/rstudiodashboard1.png)

  ![Доступ к панели мониторинга RStudio](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> В любом случае при первом входе понадобится выполнить аутентификацию дважды. В первый раз укажите идентификатор и пароль администратора кластера. Во второй раз укажите идентификатор и пароль пользователя SSH. При последующих входах достаточно ввести идентификатор и пароль пользователя SSH.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Подключение к пограничному узлу R Server

К граничному узлу R Server кластера HDInsight можно подключиться через SSH с помощью следующей команды:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Адрес `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` можно найти на портале Azure. Выберите кластер, а затем откройте элементы **Все параметры** > **Приложения** > **RServer**. Это действие выводит информацию о конечной точке SSH для граничного узла.
>
> ![Отображение конечной точки SSH для граничного узла](./media/r-server-get-started/sshendpoint.png)
>
>

Если для защиты учетной записи SSH используется пароль, появится диалог для его ввода. Если используется открытый ключ, может потребоваться параметр `-i`, чтобы указать соответствующий закрытый ключ. Например: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Дополнительные сведения см. в руководстве по [подключению к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения отобразится окно запроса, как показано ниже.

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Включение нескольких параллельных подключений пользователей

Можно создать несколько параллельных подключений пользователей, добавив пользователей для граничного узла, на котором выполняется версия RStudio Community.

При создании кластера HDInsight необходимо указать двух пользователей: для HTTP и SSH.

![Ввод учетных данных для пользователя кластера и пользователя SSH](./media/r-server-get-started/concurrent-users-1.png)

- **Имя пользователя для входа в кластер** — это пользователь для аутентификации при HTTP-подключении через шлюза HDInsight, который защищает созданные вами кластеры HDInsight. Этот пользователь HTTP нужен для доступа к пользовательскому интерфейсу Ambari, пользовательскому интерфейсу YARN и другим компонентам пользовательского интерфейса.
- **Имя пользователя Secure Shell (SSH)** — это пользователь SSH для доступа к кластеру через Secure Shell. Этот пользователь применяется в системе Linux для всех головных, рабочих и граничных узлов. SSH позволяет получить доступ к любому узлу в удаленном кластере.

Для входа в версию R Studio Server Community, используемую в Microsoft R Server в кластере HDInsight, принимаются только имя пользователя и пароль Linux. Эта версия не поддерживает передачу токенов. Если вы создали кластер и хотите использовать R Studio для доступа к нему, вам потребуется выполнить вход дважды.

1. Войдите с учетными данными пользователя HTTP через шлюз HDInsight: 

    ![Ввод учетных данных пользователя HTTP](./media/r-server-get-started/concurrent-users-2a.png)

2. Введите учетные данные SSH для входа в RStudio:
  
    ![Ввод учетных данных пользователя SSH](./media/r-server-get-started/concurrent-users-2b.png)

Сейчас при подготовке кластера HDInsight можно создать только одну учетную запись пользователя SSH. Чтобы предоставить доступ к Microsoft R Server в кластерах HDInsight нескольким пользователям, следует создать дополнительных пользователей в системе Linux.

Так как версия RStudio Server Community работает на граничном узле кластера, для этого нужно выполнить следующие три шага:

1. Вход на граничный узел с помощью созданного пользователя SSH.
2. Добавление дополнительных пользователей Linux на граничном узле.
3. Работа с версией RStudio Community от имени созданного пользователя.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>с помощью созданного пользователя SSH войти на граничный узел;

Скачайте любое средство SSH (например, PuTTY) и с помощью существующего пользователя SSH выполните вход. Чтобы войти на граничный узел, следуйте инструкциям по [подключению к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Адрес граничного узла для R Server в кластере HDInsight выгляди так: **[имя_кластера]-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Добавление на граничный узел дополнительных пользователей Linux

Чтобы добавить пользователя на граничный узел, выполните следующие команды:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Вы должны получить следующий результат: 

![Выходные данные команд sudo](./media/r-server-get-started/concurrent-users-3.png)

Когда появится запрос для текущего пароля Kerberos, проигнорируйте его и просто нажмите клавишу ВВОД. Параметр `-m` в команде `useradd` обозначает, что система создаст для пользователя домашнюю папку. Эта папка является обязательной для версия RStudio Community.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Работа с версией RStudio Community от имени созданного пользователя

Введите данные созданного пользователя для входа в RStudio.

![Страница входа в RStudio](./media/r-server-get-started/concurrent-users-4.png)

Обратите внимание, что RStudio сообщает о том, что для входа в кластер использовался новый пользователь (в нашем примере — **sshuser6**). 

![Расположение нового пользователя на панели команд RStudio](./media/r-server-get-started/concurrent-users-5.png)

Вы также можете параллельно войти с прежними учетными данными (по умолчанию — **sshuser**) в другом окне браузера.

Вы можете отправлять задания с помощью функций ScaleR. Вот пример такой команды для выполнения задания:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Обратите внимание, что переданные задания отображаются в пользовательском интерфейсе YARN от имени другого пользователя:

![Список пользователей в пользовательском интерфейсе YARN](./media/r-server-get-started/concurrent-users-6.png)

Также важно, что все новые пользователи не получают права root в системе Linux. Но они получают одни и те же права доступа ко всем файлам в удаленной файловой системе HDFS и хранилище больших двоичных объектов.


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

3. В строке ввода `>` можно ввести код R. R Server содержит пакеты, с помощью которых вы легко организуете взаимодействие с Hadoop и распределенные вычисления. Например, используйте следующую команду, чтобы просмотреть корень файловой системы по умолчанию для кластера HDInsight.

        rxHadoopListFiles("/")

4. Также можно использовать для адресации стиль хранилища больших двоичных объектов:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Использование R Server в HDI из удаленного экземпляра Microsoft R Server или клиента Microsoft R Client

Доступ к контексту вычислений Hadoop Spark в HDI можно установить из удаленного экземпляра Microsoft R Server или Microsoft R Client, запущенного на персональном компьютере или ноутбуке. Дополнительные сведения см. в разделе об использовании Microsoft R Server в качестве клиента Hadoop в статье [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Создание контекста вычислений для Spark). Для этого укажите следующие параметры при определении контекста вычислений RxSpark на локальном компьютере: dfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches и sshProfileScript. Ниже приведен пример:


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

Вы можете использовать контекст вычислений, чтобы указать, где следует выполнять вычисления — локально на граничном узле или распределенным образом на узлах кластера HDInsight.

1. В RStudio Server или консоли R (в сеансе SSH) выполните следующий код, чтобы загрузить пример данных в хранилище по умолчанию для HDInsight:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Создайте сведения о данных и определите два источника данных, чтобы можно было работать с данными.

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
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

Используя R Server, вы можете с легкостью взять имеющийся код R и запустить его на нескольких узлах кластера с помощью `rxExec`. Эта функция удобна при очистке параметров или моделировании. Следующий код является примером использования `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Если вы все еще используете контекст Spark или MapReduce, эта команда вернет число (`nodename`) рабочих узлов, на которых запущен код (`(Sys.info()["nodename"])`). Например, в кластере с четырьмя узлами будет получен примерно следующий результат:

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

Возможность, реализованная в версии R Server 9.1, обеспечивает прямой доступ к данным в Hive и Parquet для использования функций ScaleR в контексте вычислений Spark. Эти возможности доступны при использовании функций ScaleR RxHiveData и RxParquetData для источника данных. Эти функции выполняются через Spark SQL и загружают данные напрямую в структуру Spark DataFrame для анализа через ScaleR.  

Следующий пример кода демонстрирует применение этих новых функций:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Дополнительные сведения о новых функциях вы найдете в разделе справки для R Server, которую можно вызвать командой `?RxHivedata` или `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Установка дополнительных пакетов R на граничный узел

Если вы хотите установить дополнительные пакеты R на граничный узел, то можете вызвать `install.packages()` непосредственно из консоли R, подключившись к граничному узлу по протоколу SSH. Но если пакеты R нужно установить на рабочие узлы кластера, необходимо использовать действие скрипта.

Действия скриптов — это скрипты Bash, которые изменяют конфигурацию кластера HDInsight или устанавливают дополнительное программное обеспечение, например дополнительные пакеты R. Чтобы установить дополнительные пакеты с помощью действия скрипта, выполните следующее.

> [!IMPORTANT]
> Вы можете установить дополнительные пакеты R через действия скриптов только после создания кластера. Не применяйте эту процедуру в период создания кластера, так как скрипт ожидает, что R Server уже полностью установлен и настроен.
>
>

1. На [портале Azure](https://portal.azure.com)выберите свой R Server в кластере HDInsight.

2. В колонке **Параметры** выберите **Действия скрипта** > **Отправить новое**.

   ![Изображение панели "Действия скрипта"](./media/r-server-get-started/scriptaction.png)

3. В панели **Отправка действия скрипта** введите следующие сведения.

   * **Имя**: понятное имя для идентификации скрипта.

   * **URI bash-скрипта**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Заголовок**: этот флажок должен быть снят.

   * **Рабочая роль**: этот флажок должен быть снят.

   * **Zookeeper**: этот флажок должен быть снят.

   * **Граничные узлы**: этот флажок должен быть установлен.

   * **Параметры**: устанавливаемые пакеты R, например `bitops stringr arules`.

   * **Сохранить этот скрипт**: этот флажок должен быть установлен.  

   > [!NOTE]
   > По умолчанию все пакеты R устанавливаются из моментального снимка репозитория Microsoft MRAN, согласованного с установленной версией сервера R Server. Если вы хотите установить более новые версии пакетов, учитывайте риск несовместимости. Чтобы выполнить такую установку, укажите `useCRAN` в качестве первого элемента в списке пакетов, например так: `useCRAN bitops, stringr, arules`.  
   > 
   > Для некоторых пакетов R требуются дополнительные системные библиотеки Linux. Для удобства мы предварительно установили все зависимости для 100 наиболее популярных пакетов R. Если для ваших пакетов R требуются дополнительные библиотеки, скачайте пример скрипта, используемого в этой статье, и выполните шаги по установке системных библиотек. Затем передайте измененный скрипт в общедоступный контейнер больших двоичных объектов в службе хранилища Azure и используйте для установки пакетов именно этот измененный скрипт.
   >
   > Дополнительные сведения о разработке действий скриптов см. в статье [Разработка действий сценариев с помощью HDInsight](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Добавление действия сценария](./media/r-server-get-started/submitscriptaction.png)

4. Нажмите кнопку **Создать**, чтобы выполнить скрипт. После завершения скрипта указанные пакеты R будут доступны на всех рабочих узлах.


## <a name="configure-microsoft-r-server-operationalization"></a>Настройка Microsoft R Server для практического использования

Когда вы завершите моделирование данных, готовую модель можно ввести в эксплуатацию для составления прогнозов. Чтобы настроить Microsoft R Server для практического использования, выполните следующие действия:

1. Используйте команду `ssh` для граничного узла, например так: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Используйте правильный каталог для нужной версии и команду `sudo dotnet` для DLL-файла. 

   Для Microsoft R Server 9.1:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Для Microsoft R Server 9.0:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Чтобы настроить Microsoft R Server для практического использования в универсальной конфигурации, выполните следующую процедуру:

   a. Выберите `Configure R Server for Operationalization`.

   Б. Выберите `A. One-box (web + compute nodes)`.

   c. Введите пароль для пользователя `admin`.

   ![Практическое использование в универсальной конфигурации](./media/r-server-get-started/admin-util-one-box-.png)

4. В качестве дополнительного шага можно запустить диагностический тест, как описано ниже.

   a. Выберите `6. Run diagnostic tests`.

   Б. Выберите `A. Test configuration`.

   c. Введите имя пользователя `admin` и пароль из предыдущего шага настройки.

   d. Подтвердите `Overall Health = pass`.

   д. Выйдите из служебной программы администрирования.

   f. Завершите сеанс SSH.

   ![Диагностика практического использования](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Если в вычисленном контексте Spark возникают значительные задержки при попытке использовать веб-службы, созданные с использованием функций mrsdeploy, это может быть связано с отсутствием некоторых папок. В этом случае их нужно добавить. При вызове из веб-службы с помощью функций mrsdeploy приложение Spark принадлежит пользователю с именем *rserve2*. Для обхода этой проблемы:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


Итак, вы завершили настройку для практического использования. Теперь можно применить пакет mrsdeploy на клиенте R для подключения к решению для практического использования на граничном узле. После этого вы сможете использовать его функции, например [удаленное выполнение](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) и [веб-службы](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). В зависимости от того, используется ли виртуальная сеть для настройки кластера, может потребоваться туннелирование с перенаправлением портов через сеанс SSH.

### <a name="r-server-cluster-on-a-virtual-network"></a>Кластер R Server в виртуальной сети

Убедитесь, что разрешен трафик на порт 12800 граничного узла. Это позволит использовать граничный узел для подключения к решению для практического использования.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Если `remoteLogin()` не может подключиться к граничному узлу, но подключение по протоколу SSH работает нормально, — проверьте настройку правил, разрешающих трафик на порт 12800. Если и после этого проблема не исчезнет, в качестве решения можно настроить туннелирование с перенаправлением портов через SSH. Инструкции см. в следующем разделе.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Кластер RServer не настроен в виртуальной сети

Если ваш кластер не входит в виртуальную сеть или с подключением через виртуальную сеть возникают проблемы, можно использовать туннелирование с перенаправлением портов через SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Вы можете настроить то же самое в программе PuTTY:

![Подключение SSH через PuTTY](./media/r-server-get-started/putty.png)

Когда сеанс SSH установлен, трафик от порта 12800 на вашем компьютере будет перенаправляться на порт 12800 граничного узла через этот сеанс SSH. Убедитесь, что вы используете `127.0.0.1:12800` в методе `remoteLogin()`. Это метод подключается к решению для практического использования на граничном узле через механизм перенаправления портов.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Масштабирование вычислительных узлов решения для практического использования Microsoft R Server на рабочих узлах HDInsight

### <a name="decommission-the-worker-nodes"></a>Вывод рабочих узлов из эксплуатации

Управление Microsoft R Server через Yarn сейчас не поддерживается. Если вы не выведете из эксплуатации рабочие узлы, диспетчер ресурсов Yarn не сможет правильно работать, так как не будет получать информацию об используемых сервером ресурсах. Чтобы этого избежать, мы рекомендуем вывести из эксплуатации рабочие узлы перед масштабированием вычислительных узлов.

Вывод рабочих узлов из эксплуатации

1. Войдите в консоль Ambari для кластера HDI и выберите вкладку **Узлы**.
2. Выберите рабочие узлы, которые нужно вывести из эксплуатации, затем выберите **Actions (Действия)** > **Selected (Выбранные узлы) Hosts** > **Hosts (Узлы)** > **Turn ON Maintenance Mode (Включить режим обслуживания)**. Например, на следующем рисунке для вывода из эксплуатации выбраны узлы wn3 и wn4.  

   ![Снимок экрана с командами для включения режима обслуживания](./media/r-server-get-started/get-started-operationalization.png)  

3. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных)  > **Decommission** (Вывести из эксплуатации).
4. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов)  > **Decommission** (Вывести из эксплуатации).
5. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) > **Stop** (Остановить).
6. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) > **Stop** (Остановить).
7. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы)  > **Stop All Components** (Остановить все компоненты).
8. Отмените выбор рабочих узлов и выберите головные узлы.
9. Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы)  > **Restart All Components** (Перезапустить все компоненты).

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Настройка вычислительных узлов на каждом из рабочих узлов, выведенных из эксплуатации

1. Поочередно создайте сеанс подключения SSH к каждому рабочему узлу, выведенному из эксплуатации.
2. Запустите служебную программу администрирования с помощью `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Введите `1`, чтобы выбрать вариант `Configure R Server for Operationalization`.
4. Введите `c`, чтобы выбрать вариант `C. Compute node`. Это действие настраивает вычислительный узел на рабочем узле.
5. Выйдите из служебной программы администрирования.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Добавление сведений о вычислительных узлах на веб-узел

Когда вы настроите выполнение на вычислительном узле для всех рабочих узлов, выведенных из эксплуатации, вернитесь на граничный узел и добавьте в конфигурацию веб-узла Microsoft R Server IP-адреса рабочих узлов, выведенных из эксплуатации.

1. К граничному узлу можно подключиться с помощью SSH.
2. Запустите `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Найдите раздел `URIs` и добавьте в него информацию об IP-адресах и портах рабочих узлов.

    ![Командная строка для граничного узла.](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Устранение неполадок

Если у вас возникли проблемы при создании кластеров HDInsight, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Дополнительная информация

Теперь вы знакомы с процедурой создания кластера HDInsight с сервером R Server. Вы также узнали об использовании консоли R в сеансе SSH. В следующих разделах описаны другие способы работы с R Server в HDInsight и его администрирования:

* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)](r-server-storage.md)

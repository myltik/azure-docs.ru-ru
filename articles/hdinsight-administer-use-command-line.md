<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters using Cross-Platform Command-Line | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hadoop, administration" description="Learn how to use the Cross-Platform Command-Line Interface to manage Hadoop clusters in HDIsight on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer Hadoop clusters using the Cross-platform Command-line Interface" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Управление кластерами Hadoop в HDInsight с использованием межплатформенного интерфейса командной строки

В этой статье вы узнаете, как использовать межплатформенный интерфейс командной строки для управления кластерами Hadoop в HDInsight. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux.

Средство командной строки имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

Эта статья посвящена только интерфейсу командной строки в ОС Windows. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][Использование средств командной строки Azure для Mac и Linux]. Полную справочную документацию см. в разделе [Средства командной строки Windows Azure для Mac и Linux][Средства командной строки Windows Azure для Mac и Linux].

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

-   **Подписка Azure**. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][Варианты приобретения], [Предложения для участников][Предложения для участников] или [Бесплатное пробное использование][Бесплатное пробное использование].

## Содержание

-   [Установка][Установка]
-   [Загрузка и импорт файла publishsettings учетной записи Azure][Загрузка и импорт файла publishsettings учетной записи Azure]
-   [Подготовка кластера][Подготовка кластера]
-   [Подготовка кластера с использованием файла конфигурации][Подготовка кластера с использованием файла конфигурации]
-   [Отображение кластеров][Отображение кластеров]
-   [Удаление кластера][Удаление кластера]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="installation"></span></a> Установка

Интерфейс командной строки может быть установлен с использованием *диспетчера пакетов Node.js (NPM)* или установщика Windows.

**Чтобы установить интерфейс командной строки с помощью NPM, выполните следующие действия**

1.  Перейдите к **www.nodejs.org**
2.  Щелкните **УСТАНОВИТЬ** и выполните инструкции с использованием параметров по умолчанию.
3.  Откройте **окно командной строки** (или *окно командной строки Azure*, или *окно командной строки разработчика VS2012*) на своей рабочей станции.
4.  Выполните следующую команду в окне командной строки.

        npm install -g azure-cli

    > [WACOM.NOTE] Если возникло сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменой среды PATH следующих путей: *C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* or *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  Выполните следующую команду, чтобы проверить установку:

        azure hdinsight -h

    Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Чтобы установить интерфейс командной строки с помощью установщика windows, выполните следующие действия**

1.  Перейдите по адресу: **<http://azure.microsoft.com/ru-ru/downloads/>**.
2.  Прокрутите вниз до раздела **Средства командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте инструкциям мастера установщика веб-платформы.

## <span id="importsettings"></span></a> Загрузка и импорт файла publishsettings учетной записи Azure

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить из файла publishsettings через портал Azure. Файл publishsettings можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать файл publishsettings только один раз.

> [WACOM.NOTE] Файл publishsettings содержит конфиденциальные сведения. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.

**Загрузка и импорт файла publishsettings**

1.  Откройте **окно командой строки**.
2.  Выполните следующую команду, чтобы загрузить файл publishsettings:

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    Команда отображает инструкции по загрузке файла, включая URL-адрес.

3.  Откройте **Internet Explorer** и перейдите к URL-адресу указанному в окне командой строки.
4.  Нажмите кнопку **Сохранить** для сохранения файла на рабочей станции.
5.  В окне командной строки выполните следующую команду, чтобы импортировать файл publishsettings:

        azure account import <file>

    На предыдущем снимке файл publishsettings был сохранен в папку C:\\HDInsight на рабочей станции.

## <span id="provision"></span></a>Подготовка кластера HDInsight

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure.

После импорта файла publishsettings используйте следующую команду для создания учетной записи хранения:

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] Учетная запись хранения должна быть связана с тем же центром обработки данных. В настоящее время можно подготовить только кластеры HDInsight в следующих центрах обработки данных:

> -   Юго-Восточная Азия
> -   Северная Европа
> -   Западная Европа
> -   Восток США
> -   Запад США

Сведения о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание учетной записи хранения][Создание учетной записи хранения].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

Подробную информацию о получении сведений с использованием портала управления см. в *Практическом руководстве: Просмотр, копирование и повторное создание ключей доступа к хранилищу*, раздел [Управление учетными записями хранения][Управление учетными записями хранения].

Команда *azure hdinsight cluster create* создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

## <span id="provisionconfigfile"></span></a> Подготовка кластера HDInsight с использованием файла конфигурации

Как правило, необходимо подготовить кластер HDInsight, выполнить в нем задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

    azure hdinsight cluster config create <file>
     
    azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
     
    azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
           --storageAccountKey "<StorageAccountKey>"
     
    azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
           --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
     
    azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
           --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
     
    azure hdinsight cluster create --config <file>
         

![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

## <span id="listshow"></span></a> Отображение сведений о кластере

Используйте следующие команды для отображения сведений о кластере:

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][HDI.CLIListCluster]

## <span id="delete"></span></a> Удаление кластера

Используйте следующую команду для удаления кластера:

    azure hdinsight cluster delete <ClusterName>

## <span id="nextsteps"></span></a>Дальнейшие действия

В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Администрирование HDInsight с использованием портала управления][Администрирование HDInsight с использованием портала управления]
-   [Администрирование HDInsight с использованием PowerShell][Администрирование HDInsight с использованием PowerShell]
-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Использование средств командной строки Azure для Mac и Linux][Использование средств командной строки Azure для Mac и Linux]
-   [Средства командной строки Azure для Mac и Linux][Средства командной строки Windows Azure для Mac и Linux]

  [Использование средств командной строки Azure для Mac и Linux]: ../xplat-cli/
  [Средства командной строки Windows Azure для Mac и Linux]: ../command-line-tools/
  [Варианты приобретения]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
  [Предложения для участников]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
  [Бесплатное пробное использование]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
  [Установка]: #installation
  [Загрузка и импорт файла publishsettings учетной записи Azure]: #importsettings
  [Подготовка кластера]: #provision
  [Подготовка кластера с использованием файла конфигурации]: #provisionconfigfile
  [Отображение кластеров]: #listshow
  [Удаление кластера]: #delete
  [Дальнейшие действия]: #nextsteps
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
  [Создание учетной записи хранения]: ../storage-create-storage-account/
  [Управление учетными записями хранения]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Отображение кластеров"
  [Администрирование HDInsight с использованием портала управления]: ../hdinsight-administer-use-management-portal/
  [Администрирование HDInsight с использованием PowerShell]: ../hdinsight-administer-use-powershell/
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/

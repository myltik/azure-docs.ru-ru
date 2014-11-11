<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров

В этой статье вы познакомитесь с различными способами настраиваемой подготовки кластера Hadoop в Azure HDInsight - с использованием портала управления Azure, PowerShell, средств командной строки или пакета SDК для HDInsight .NET. В статье рассматривается подготовка кластеров Hadoop. Инструкции по подготовка кластера HBase см. в разделе [Подготовка кластера HBase в HDInsight][Подготовка кластера HBase в HDInsight]. Для получения информации о выборе того или иного кластера см. в разделе [В чем разница между Hadoop и HBase?][В чем разница между Hadoop и HBase?].

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, что при упоминании кластеров мы всегда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру ведущий/ведомый с ведущим (также называемым головным узлом или узлом имени) и любым количеством ведомых (также неназываемых узлами данных). Дополнительную информацию см. в разделе [Apache Hadoop][Apache Hadoop].

Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Для получения дополнительной информации см. раздел [Введение для Hadoop в HDInsight][Введение для Hadoop в HDInsight].

В этой статье приводятся указания по различным способам подготовки кластера. Сведения о быстрой подготовке кластера см. в разделе [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight].

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

-   Подписка Azure. Azure — это платформа на основе подписок. Командлеты HDInsight PowerShell предназначены для выполнения задач по подписке. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][Варианты приобретения], [Предложения для участников][Предложения для участников] или [Бесплатное пробное использование][Бесплатное пробное использование].

## Содержание

-   [Использование портала управления Azure][Использование портала управления Azure]
-   [Использование Azure PowerShell][Использование Azure PowerShell]
-   [Использование кроссплатфомернной командной строки][Использование кроссплатфомернной командной строки]
-   [Использование пакета HDInsight .NET SDK][Использование пакета HDInsight .NET SDK]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="portal"></span></a> Использование портала управления Azure

Кластер HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight]. Сведения о создании учетной записи хранения Azure см. в разделе [Создание учетной записи хранения][Создание учетной записи хранения].

> [WACOM.NOTE] В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

**Чтобы создать кластер HDInsight с использованием возможности настраиваемого создания, выполните следующие действия**

1.  Выполните вход на [Портал управления Azure][Портал управления Azure].
2.  В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3.  На странице **Сведения о кластере** введите или выберите следующие значения:

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство</th>
    <th align="left">Значение</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Имя кластера</td>
    <td align="left"><p>Имя кластера.</p>
    <ul>
    <li>DNS-имя должно начинаться и заканчиваться с буквы или цифры и может содержать дефисы.</li>
    <li>Поле должно представлять собой строку от 3 до 63 символов.</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Тип кластера</td>
    <td align="left">Для типа кластера выберите <strong>Hadoop</strong>.</td>
    </tr>
    <tr class="odd">
    <td align="left">Версия HDInsight</td>
    <td align="left">Выберите версию. Для Hadoop по умолчанию используется HDInsight версии 3.1, которая использует Hadoop 2.4.</td>
    </tr>
    </tbody>
    </table>

    Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4.  На странице **Настройка кластера** введите или выберите следующие значения:

    | Имя                     | Значение                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Узлы данных             | Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом.                                                                                                                                                                                                                                                                                                                                                                                                               
                               Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.                                                                                                                                                                                                                                                                                                                                                                      |
    | Регион/виртуальная сеть | Выберите тот же регион, что и для учетной записи хранения, созданной в предыдущей процедуре. HDInsight требует размещения учетной записи хранения в том же регионе. При последующей настройке можно выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь. Доступны следующие регионы: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**. |

5.  На странице **Настройка пользователя кластера** введите следующие значения:

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | Свойство                            | Значение                                                                                                                                                                                                                                                                                                                                |
    |-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Имя пользователя                    | Укажите имя пользователя кластера HDInsight.                                                                                                                                                                                                                                                                                            |
    | Пароль/подтверждение пароля         | Укажите пароль пользователя кластера HDInsight.                                                                                                                                                                                                                                                                                         |
    | Ввести куст или метахранилище Oozie | Установите флажок, чтобы указать базу данных SQL, которая будет использоваться в качестве метахранилища Hive/Oozie, в том же центре обработки данных, что и кластер. Это может оказаться полезным, если необходимо сохранить метаданные заданий Hive/Oozie даже после удаления кластера.                                                |
    | База данных метахранилища           | Укажите базу данных SQL Azure, которая будет использоваться в качестве метахранилища для Hive/OOzie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL в том же центре обработки данных, который указан на странице **Сведения о кластере**. |
    | Пользователь базы данных            | Укажите пользователя базы данных SQL, от имени которого будет выполняться подключение к базе данных.                                                                                                                                                                                                                                    |
    | Пароль пользователя базы данных     | Укажите пароль пользователя базы данных SQL.                                                                                                                                                                                                                                                                                            |

    > [WACOM.NOTE] База данных Azure SQL, используемая в качестве метахранилища, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, **Да** и **Сохранить**.

    Щелкните стрелку вправо.

6.  На странице **Учетная запись хранения** введите следующие значения:

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство</th>
    <th align="left">Значение</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Учетная запись хранения</td>
    <td align="left">Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
    <ul>
    <li>Использовать существующее хранилище</li>
    <li>Создать новое хранилище</li>
    <li>Использовать хранилище другой подписки</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Имя учетной записи</td>
    <td align="left"><ul>
    <li>Если используется существующее хранилище, выберите существующую учетную запись хранения для параметра <strong>Имя учетной записи</strong>. В раскрывающемся списке указываются только учетные записи хранения, расположенные в том же центре обработки данных, в котором выполняется подготовка кластера.</li>
    <li>При выборе параметра <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище из другой подписки</strong>, необходимо указать имя учетной записи хранения.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Ключ учетной записи</td>
    <td align="left">Если используется параметр <strong>Использовать учетную запись из другой подписки</strong>, необходимо указать ключ этой учетной записи хранения.</td>
    </tr>
    <tr class="even">
    <td align="left">Контейнер по умолчанию</td>
    <td align="left"><p>Задает контейнер по умолчанию в учетной записи хранения, который используется в качестве файловой системы по умолчанию для кластера HDInsight. Если выбирается параметр <strong>Использовать существующее хранилище</strong> для поля <strong>Учетная запись хранения</strong>, и в этой учетной записи нет существующих контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с иным, чем у кластера именем, можно также использовать и этот контейнер.</p>
    <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Дополнительные учетные записи хранения</td>
    <td align="left">HDInsight поддерживает несколько учетных записей хранения. Нет ограничений на дополнительную учетную запись хранения, которая может использоваться в кластере. Тем не менее, при создании кластера с использованием портала управления ограничением является семь единиц вследствие ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения к мастеру, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td>
    </tr>
    </tbody>
    </table>

    Щелкните стрелку вправо.

7.  На странице **Учетная запись хранения** введите сведения о дополнительной учетной записи хранения:

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.

    Щелкните галочку, чтобы начать подготовку кластера. После завершения процесса подготовки в столбце состояния будет отображаться **Работает**.

    > [WACOM.NOTE] После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.

## <span id="powershell"></span></a> Использование Azure PowerShell

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе рассматривается подготовка кластера HDInsight Сведения о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Windows Azure PowerShell][Установка и настройка Windows Azure PowerShell]. Дополнительные сведения об использовании PowerShell с HDInsight см. в разделе [Администрирование HDInsight с использованием PowerShell][Администрирование HDInsight с использованием PowerShell]. Список командлетов HDInsight PowerShell см. в [Справочнике по командлетам HDInsight][Справочнике по командлетам HDInsight].

Для подготовки кластера HDInsight с помощью PowerShell необходимы следующие процедуры:

-   Создание учетной записи хранения Azure
-   Создание контейнера BLOB-объектов Azure
-   Создание кластера HDInsight

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight.

**Создание учетной записи хранения Azure**

-   Выполните следующие команды в окне консоли Azure PowerShell:

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды PowerShell для получения нужных сведений:

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Создание контейнера хранилища Blob-объектов Azure**

-   Выполните следующие команды в окне консоли Azure PowerShell:

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

-   Выполните следующие команды в окне консоли Azure PowerShell:

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    При появлении запроса введите учетные данные для кластера. На подготовку кластера может уйти несколько минут.

    ![HDI.CLI.Provision][HDI.CLI.Provision]

**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации:**

При подготовке кластера, можно использовать другие параметры конфигурации, такие как подключение к нескольким хранилищам BLOB-объектов Azure или использование базы данных Azure AQL для метахранилищ Hive и Oozie. Эта позволяет разделить время жизни данных и метаданных от времени жизни кластера.

-   Выполните следующие команды в окне Windows PowerShell:

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] База данных Azure SQL, используемая в качестве метахранилища, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, **Да** и **Сохранить**.

**Чтобы отобразить список кластеров HDInsight, выполните следующие действия**

-   Выполните следующие команды в окне консоли Azure PowerShell, чтобы указать кластер HDInsight в списке и убедиться, что он был успешно создан:

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Использование кроссплатфомернной командной строки

Другим вариантом подготовки кластера HDInsight является интерфейс кроссплатформенной командной строки. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Средство командной строки имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу [https://github.com/WindowsAzure/azure-sdk-tools-xplat][https://github.com/WindowsAzure/azure-sdk-tools-xplat]. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][Использование средств командной строки Azure для Mac и Linux]. Полную справочную документацию см. в разделе [Средства командной строки Windows Azure для Mac и Linux][Средства командной строки Windows Azure для Mac и Linux]. Эта статья посвящена только интерфейсу командной строки в ОС Windows.

Для подготовки кластера HDInsight с помощью кроссплатформенной командной строки необходимы следующие процедуры:

-   Установите кроссплатформенную командную строку
-   Загрузите и импортируйте параметры публикации учетной записи Azure
-   Создание учетной записи хранения Azure
-   Подготовка кластера

Интерфейс командной строки может быть установлен с использованием *диспетчера пакетов Node.js (NPM)* или установщика Windows. Майкрософт рекомендует проводить установку с использованием только одного из двух параметров.

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

**Загрузка и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [WACOM.NOTE] Файл publishsettings содержит конфиденциальные сведения. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.

1.  Откройте **окно командой строки**.
2.  Выполните следующую команду для загрузки файла параметров публикации.

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    Команда запускает веб-страницу для загрузки с нее файла параметров публикации.

3.  При появлении запроса на сохранение файла щелкните **Сохранить** и укажите место, в котором должен быть сохранен файл.
4.  В окне командной строки выполните следующую команду для импорта файла параметров публикации.

        azure account import <file>

    На предыдущем снимке файл параметров публикации был сохранен в папку C:\\HDInsight на рабочей станции.

**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

-   Из окна командной строки выполните следующую команду для создания учетной записи хранения Azure:

        azure storage account create [options] <StorageAccountName>

    При появление запроса на указание места, выберите местоположение, в котором может быть подготовлен кластер HDINsight. Хранилище должно находиться в одном местоположении с кластером HDInsight. В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

Сведения о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание учетной записи хранения][Создание учетной записи хранения].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Подробную информацию о получении сведений с использованием портала управления см. в *Практическом руководстве: Просмотр, копирование и повторное создание ключей доступа к хранилищу*, раздел [Управление учетными записями хранения][Управление учетными записями хранения].

Кластер HDInsight также требует наличия контейнера в учетной записи хранения. Если указанная учетная запись хранения еще не имеет контейнер, то команда *azure hdinsight cluster create* запрашивает у вас имя контейнера, а также создает его. Однако, для создания контейнера заранее, можно использовать следующую команду:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

-   Введите в окне командной строки следующую команду:

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

**Чтобы подготовить кластер HDInsight с использованием файла конфигурации, выполните следующие действия:**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

-   Введите в окне командной строки следующие команды:

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] База данных Azure SQL, используемая в качестве метахранилища, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, **Да** и **Сохранить**.

    ![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

**Чтобы показать сведения о кластере, выполните следующие действия**

-   Используйте следующие команды для отображения сведений о кластере:

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][HDI.CLIListCluster]

**Чтобы удалить кластер, выполните следующие действия**

-   Используйте следующую команду для удаления кластера:

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Использование пакета HDInsight .NET SDK

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET.

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

-   Установка пакета SDK для HDInsight .NET
-   Создание самозаверяющего сертификата
-   Создание консольного приложение
-   Выполнение приложения

**Установка пакета HDInsight .NET SDK**

Последнюю опубликованную сборку пакета SDK можно установить из [NuGet][NuGet]. Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

1.  Создайте самозаверяющий сертификат, который используется для проверки подлинности запросов. Чтобы создать сертификат, можно использовать IIS или команду [makecert][makecert].

2.  Выполните обзор местоположения сертификата, щелкните сертификат правой кнопкой мыши, щелкните **Установить сертификат** и установите сертификат в личное хранилище компьютера. Измените свойства сертификата, чтобы назначить ему понятное имя.

3.  Выполните импорт сертификата в портал управления Azure. В портале управления щелкните **Параметры** в нижней левой части страницы, а затем нажмите **Управление сертификатами**. В нижней части страницы щелкните **Отправить** и выполните указания для отправки файла .cer, созданного на предыдущем шаге

    ![HDI.ClusterCreate.UploadCert][HDI.ClusterCreate.UploadCert]

**Создание консольного приложения Visual Studio**

1.  Откройте Visual Studio 2013.

2.  В меню "Файл" щелкните **Создать**, затем щелкните **Проект**.

3.  В окне "Новый проект" введите или выберите следующие значения:

    | Свойство  | Значение                     |
    |-----------|------------------------------|
    | Категория | Templates/Visual C#/Windows |
    | Шаблон    | Консольное приложение        |
    | Имя       | CreateHDICluster             |

4.  Нажмите кнопку **ОК**, чтобы создать проект.

5.  В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.

6.  Для установки пакетов выполните следующие команды на консоли:

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7.  В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл.

8.  Добавьте в начало файла следующие инструкции using:

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  В функции Main() скопируйте и вставьте следующий код:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Замените переменные в начале функции main().

**Выполнение приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.

## <span id="nextsteps"></span></a>Дальнейшие действия

В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Администрирование HDInsight с использованием PowerShell][Администрирование HDInsight с использованием PowerShell]
-   [Отправка заданий Hadoop программными средствами][Отправка заданий Hadoop программными средствами]
-   [Документация по пакету Azure HDInsight SDK][Документация по пакету Azure HDInsight SDK]

  [Подготовка кластера HBase в HDInsight]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-get-started/
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [Введение для Hadoop в HDInsight]: ../hdinsight-introduction/
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Варианты приобретения]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
  [Предложения для участников]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
  [Бесплатное пробное использование]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
  [Использование портала управления Azure]: #portal
  [Использование Azure PowerShell]: #powershell
  [Использование кроссплатфомернной командной строки]: #cli
  [Использование пакета HDInsight .NET SDK]: #sdk
  [Дальнейшие действия]: #nextsteps
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Создание учетной записи хранения]: ../storage-create-storage-account/
  [Портал управления Azure]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Установка и настройка Windows Azure PowerShell]: ../install-configure-powershell/
  [Администрирование HDInsight с использованием PowerShell]: ../hdinsight-administer-use-powershell/
  [Справочнике по командлетам HDInsight]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [https://github.com/WindowsAzure/azure-sdk-tools-xplat]: https://github.com/Azure/azure-sdk-tools-xplat
  [Использование средств командной строки Azure для Mac и Linux]: ../xplat-cli/
  [Средства командной строки Windows Azure для Mac и Linux]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Управление учетными записями хранения]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Отображение кластеров"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/ru-ru/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [Отправка заданий Hadoop программными средствами]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Документация по пакету Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

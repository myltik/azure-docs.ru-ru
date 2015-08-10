<properties 
   pageTitle="Настраиваемая подготовка кластеров Hadoop в HDInsight | Microsoft Azure" 
   description="Узнайте, как выполнить настраиваемую подготовку кластеров Azure HDInsight с помощью портала Azure, Azure PowerShell, командной строки или пакета SDK для HDInsight .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/22/2015"
   ms.author="jgao"/>

#Подготовка кластеров Hadoop в HDInsight

Узнайте, как спланировать подготовку кластеров HDInsight.

**Предварительные требования:**

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Базовые параметры конфигурации


- **Имя кластера**

	Имя кластера используется для его идентификации. Оно должно соответствовать следующим требованиям:
	
	- поле должно представлять собой строку длиной от 3 до 63 символов;
	- поле может содержать только буквы, цифры и дефисы.

- **Название подписки**

	Кластер HDInsight привязывается к одной подписке Azure.
 
- **Операционная система**

	Вы можете подготовить кластеры HDInsight на одной из следующих операционных систем: **Windows (Windows Server 2012 R2 Datacenter)**, **Linux (Ubuntu 12.04 LTS для Linux; предварительная версия)**. HDInsight позволяет настраивать кластеры для Linux на базе Azure. Настройте кластер Linux, если вы знакомы с Linux или Unix, выполняете миграцию из существующего решения Hadoop на платформе Linux или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux. Дополнительную информацию см. в разделе [Приступая к работе с Hadoop в Linux на платформе HDInsight](hdinsight-hadoop-linux-get-started.md).


- **Версия HDInsight**

	Этот параметр используется для определения версии HDInsight для кластера. Дополнительные сведения см. в разделе [Версии и компоненты кластеров Hadoop в HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Тип кластера** и **размер кластера (узлы данных)**

	HDInsight позволяет клиентам развертывать различные типы кластеров для рабочих нагрузок анализа различных данных. На сегодняшний день доступны следующие типы кластеров:
	
	- кластеры Hadoop — для запросов и анализа;
	- кластеры HBase — для NoSQL;
	- кластеры Storm — для обработки событий в реальном времени;
	- кластеры Spark (предварительная версия) — для обработки в оперативной памяти, интерактивных запросов, потоков и машинного обучения.

	![Кластеры HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)
 
	> [AZURE.NOTE]*Кластер Azure HDInsight* также называется *кластером Hadoop в HDInsight* или *кластером HDInsight*. Иногда также используется термин *кластер Hadoop*. Все они обозначают кластеры Hadoop, размещенные в среде Microsoft Azure.

	Разные узлы кластера каждого типа могут выполнять разные роли. Клиенты могут настраивать размеры узлов в соответствии с нагрузкой в рамках определенной роли. Например, рабочие узлы кластера Hadoop можно подготовить с большим объемом памяти, если она активно используется для выполнения аналитических задач.

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Кластеры Hadoop для HDInsight развертываются с двумя ролями:

	- головной узел (два узла);
	- узел данных (по крайней мере один узел).

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Кластеры HBase для HDInsight развертываются с тремя ролями: головные серверы (два узла), региональные серверы (по крайней мере один узел) и узлы Master/Zookeeper (три узла).
	
	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Кластеры Storm для HDInsight развертываются с тремя ролями: узлы Nimbus (два узла), наблюдающие серверы (по крайней мере один узел) и узлы Zookeeper (три узла).
	

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Кластеры Spark для HDInsight развертываются с тремя ролями: головной узел (два узла), рабочий узел (по крайней мере один узел) и узлы Zookeeper (три узла; уровень A1 — бесплатно).

	Клиенты оплачивают использование этих узлов на протяжении всего срока существования кластера. Начисление начинается сразу после создания кластера и завершается при его удалении (нельзя высвободить память, выделенную для кластеров, или приостановить их работу). На размер оплаты влияет размер кластера. Для учебных целей рекомендуется использовать один узел данных. Дополнительные сведения о ценах на HDInsight см. в разделе [Цены HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Максимальный размер кластера зависит от подписки Azure. Чтобы увеличить лимит, обратитесь в службу поддержки по вопросам выставления счетов.
	
- **Регион или виртуальная сеть (расположение)**

	![Регионы Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Чтобы просмотреть поддерживаемые регионы, щелкните раскрывающийся список **Регион** на странице [Цены на HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Размер узла**

	![размеры узла виртуальной машины hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Выберите размеры виртуальной машины для узлов. Дополнительную информацию см. в разделе [Размеры для облачных служб](cloud-services-sizes-specs.md).

	Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.


- **Пользователи HDInsight**

	Кластеры HDInsight позволяют во время подготовки настроить две учетные записи пользователей.

	- Пользователь HTTP. По умолчанию используется имя admin и базовая конфигурация на портале Azure.
	- Пользователь RDP (кластеры Windows). Она используется для подключения к кластеру через RDP. При создании учетной записи необходимо задать дату окончания срока действия не позднее 90 дней с текущей даты. 
	- Пользователь SSH (кластеры Linux). Она используется для подключения к кластеру через SSH. Дополнительные учетные записи пользователей SSH можно создать после создания кластера, следуя инструкциям в статье [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  
 

- **Учетная запись хранения Azure.**


	HDFS использует для хранения данных несколько локальных дисков в кластере, а HDInsight — хранилище BLOB-объектов Azure. Хранилище BLOB-объектов Azure — это надежное, универсальное решение, которое полностью интегрируется с HDInsight. С помощью интерфейса распределенной файловой системы Hadoop (HDFS) все компоненты HDInsight могут напрямую взаимодействовать со структурированными или неструктурированными данными в хранилище больших двоичных объектов. Хранение данных в хранилище BLOB-объектов позволяет безопасно и без потери пользовательских данных удалять используемые для расчетов кластеры HDInsight.
	
	В процессе настройки необходимо задать учетную запись хранения Azure и контейнер для хранения BLOB-объектов. В некоторых сценариях подготовки учетную запись и контейнер потребуется создать заранее. Кластер будет использовать этот контейнер в качестве хранилища по умолчанию. Вы также можете указать дополнительные учетные записи хранения Azure (связанные хранилища), к которым кластер получит доступ. Кластер также сможет получать доступ к любым контейнерам BLOB-объектов, настроенным с полным общим доступом для чтения или общим доступом для чтения только на уровне BLOB-объектов. Дополнительные сведения об ограничениях доступа см. в разделе [Управление доступом к ресурсам хранилища Azure](storage-manage-access-to-resources.md).

	![Хранилище HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)
	
	>[AZURE.NOTE]BLOB-объекты группируются в контейнерах следующим образом.
	
	![Хранилище blob-объектов Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING]Не используйте один контейнер хранилища больших двоичных объектов для нескольких кластеров. Это не поддерживается.
	
	Дополнительную информацию об использовании дополнительных хранилищ больших двоичных объектов см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-use-blob-storage.md).

- **Метахранилище Hive/Oozie**

	Метахранилище содержит метаданные Hive и Oozie, такие как таблицы, секции, схемы и столбцы Hive. Использование метахранилища помогает сохранять метаданные Hive и Oozie, чтобы не создавать повторно таблицы Hive или задания Oozie при подготовке нового кластера. Для хранения подобной информации Hive по умолчанию использует встроенную базу данных SQL Azure. Встроенная база данных не сохраняет метаданные при удалении кластера. Предположим, вы подготовили кластер с метахранилищем Hive и создали некоторые таблицы Hive. Если после удаления кластера вы повторно создадите его с использованием того же метахранилища Hive, то таблицы Hive, созданные в исходном кластере, будут доступны.

## Расширенные параметры конфигурации

>[AZURE.NOTE]Информация в этом разделе применима только к кластерам HDInsight на базе Windows.

### Настройка кластеров HDInsight

В некоторых случаях требуется изменить файлы конфигурации. Вот некоторые из них:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

При повторном развертывании образа изменения в кластере не сохраняются. Дополнительные сведения см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Чтобы они сохранялись в течение всего времени существования кластера, можно в процессе подготовки изменить файлы конфигурации кластера HDInsight.

Ниже приведен пример сценария Azure PowerShell, который меняет конфигурацию Hive:

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Вот еще несколько примеров изменения других файлов конфигурации:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
Дополнительные сведения см. в статье Азима Уддина [Дополнительная настройка при подготовке кластера HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Настройка кластеров с помощью действия сценария

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время подготовки. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться посредством портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в статье [Настройка кластера HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).


### Использование виртуальных сетей Azure

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть-сеть» или «точка-сеть») при помощи виртуальной частной сети (VPN).

	Конфигурация «сервер-сервер» позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация «точка-сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
>[AZURE.NOTE]Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети. Чтобы проверить, является ли существующая виртуальная сеть Azure географически привязанной, используйте командлет Azure PowerShell Get-AzureVNetConfig. Если у вашей виртуальной сети нет географической привязки, вы можете:
>
> - Экспортировать конфигурацию существующей виртуальной сети, а затем создать новую виртуальную сеть. У всех новых виртуальных сетей по умолчанию есть географическая привязка.
> - Выполнить миграцию в географически привязанную виртуальную сеть. Дополнительные сведения см. в статье [Перенос существующих служб на региональный уровень](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

## Инструменты подготовки

- Портал Azure
- Azure PowerShell
- ПАКЕТ SDK .NET
- Интерфейс командной строки

### Использование портала Azure

Описание полей см. в разделах [Базовые параметры конфигурации] и [Расширенные параметры конфигурации].

**Создание кластера HDInsight с использованием параметра «Настраиваемое создание»**

1. Войдите на [портал Azure][azure-management-portal].
2. В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Сведения о кластере** введите или выберите следующие значения:

	- Имя кластера,
	- Название подписки
	- Тип кластера
	- Операционная система
	- Версия HDInsight

4. На странице **Настройка кластера** введите или выберите следующие значения:

	- Узлы данных
	- Регион/виртуальная сеть
	- Размер головного узла
	- Размер узла данных

5. На странице **Настройка пользователя кластера** введите следующие значения:

	- Имя пользователя HTTP
	- Пароль и подтверждение пароля HTTP
	- Включить удаленный рабочий стол для кластера
	- Ввести метахранилище Hive или Oozie

6. Если на предыдущем экране вы решили задать метахранилище Hive или Oozie, на странице **Настройка метахранилища Hive и Oozie** укажите следующие значения:

	- База данных метахранилища Hive
	- Пользователь базы данных
	- Пароль пользователя базы данных
	- База данных метахранилища Oozie
	- Пользователь базы данных
	- Пароль пользователя базы данных

7. На странице **Учетная запись хранения** введите следующие значения:

	- Учетная запись хранения
	- Имя учетной записи
	- Контейнер по умолчанию
	- Дополнительные учетные записи хранения

8. На странице **Действия сценариев** щелкните **Добавить действие сценария**, чтобы указать информацию о пользовательском сценарии, который необходимо выполнить для настройки кластера при его создании. Дополнительную информацию см. в разделе [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

	- Имя. Укажите имя для действия сценария.
	- URI сценария. Укажите универсальный идентификатор ресурса (URI) для сценария, который вызывается для настройки кластера.
	- Тип узла. Укажите узлы, на которых выполняется сценарий настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только узлы данных</b>.
	- Параметры. Если для выполнения сценария требуются какие-либо параметры, задайте их.</td></tr>

	Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов, щелкните флажок, чтобы начать подготовку кластера.

### Использование Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE]Сценарии в этом разделе можно использовать для настройки кластеров HDInsight под виртуальную сеть Azure, однако саму виртуальную сеть они создать не смогут. Дополнительную информацию о создании виртуальной сети Azure см. в разделе [Задачи конфигурации виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Для подготовки кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

Вы можете выполнять сценарии с помощью консоли Windows PowerShell или интегрированной среды сценариев (ISE) Windows PowerShell.

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight.

**Подключение к учетной записи Azure**

	Add-AzureAccount

Появится запрос на ввод учетных данных учетной записи Azure.

**Создание учетной записи хранения Azure**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа этой учетной записи, можно использовать следующие команды Windows PowerShell для получения нужной информации:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Создание контейнера хранилища больших двоичных объектов Azure**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

> [AZURE.NOTE]Для изменения значений переменных в кластере HDInsight мы советуем использовать только командлеты Azure PowerShell. Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений. Значения настроек, заданные с помощью Azure PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне консоли Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Команды $hadoopUserName и $hadoopUserPassword используются для создания учетной записи пользователя Hadoop для кластера. Эта учетная запись будет использоваться для подключения к кластеру и выполнения заданий. При подготовке кластера с использованием параметра «Быстрое создание» на портале Azure имя пользователя Hadoop по умолчанию — admin. Не путайте эту учетную запись с учетной записью пользователя для подключения к удаленному рабочему столу. Учетная запись пользователя для подключения к удаленному рабочему столу должна отличаться от учетной записи пользователя Hadoop. Дополнительную информацию см. в разделе [Управление кластерами Hadoop в HDInsight с использованием портала управления Azure][hdinsight-admin-portal].

	На подготовку кластера может уйти несколько минут.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации**

При подготовке кластера можно использовать другие параметры конфигурации, такие как подключение к нескольким контейнерам хранилища больших двоичных объектов Azure, использование виртуальной сети или использование базы данных SQL Azure для метахранилищ Hive и Oozie. Это позволяет разделить время существования данных и метаданных и время существования кластера.

> [AZURE.NOTE]Для изменения значения переменных в кластере HDInsight мы советуем использовать только командлеты Windows PowerShell. Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений. Значения настроек, заданные с помощью Azure PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне Windows PowerShell:

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

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

**Отображение списка кластеров HDInsight**

- Выполните следующие команды в окне консоли Azure PowerShell, чтобы указать кластер HDInsight в списке и убедиться, что он был успешно создан:

		Get-AzureHDInsightCluster -Name <ClusterName>


### Использование Azure CLI

> [AZURE.NOTE]С 8.29.2014 интерфейс командной строки Azure не может использоваться для связывания кластера с виртуальной сетью Azure.

Другим вариантом подготовки кластера HDInsight является использование интерфейса командной строки Azure. Интерфейс командной строки Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. CLI можно установить из следующих расположений:

- **пакет SDK для Node.js** — <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>;
- **Azure CLI** — <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Общее руководство по использованию интерфейса командной строки Azure CLI см. в статье [Интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli.md).

Приведенные ниже инструкции помогут в установке Azure CLI на базе Linux и Windows с дальнейшим использованием командной строки для подготовки кластера.

- [Настройка Azure CLI для Linux](#clilin)
- [Настройка Azure CLI для Windows](#cliwin)
- [Подготовка кластеров HDInsight с помощью Azure CLI](#cliprovision)

#### <a id="clilin"></a>Настройка интерфейса командной строки Azure для Linux

Выполните следующие процедуры, чтобы настроить компьютер с ОС Linux для использования интерфейса командной строки Azure (Azure CLI):

- Установите интерфейс командной строки Azure с помощью диспетчера пакетов Node.js (NPM)
- Подключение к подписке Azure

**Чтобы установить интерфейс командной строки Azure с помощью NPM**

1.	Откройте окно терминала на компьютере с ОС Linux и выполните следующую команду:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Подключение к подписке Azure**

Прежде чем использовать интерфейс командной строки Azure, необходимо настроить связь между рабочей станцией и Azure. Информация о подписке Azure используется интерфейсом командной строки Azure для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который будет использоваться интерфейсом командной строки Azure для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [AZURE.NOTE]Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. В Windows измените свойства папки или используйте шифрование дисков BitLocker.


1.	Откройте окно терминала.
2.	Выполните следующую команду для входа в подписку Azure:

		azure account download

	![HDI. Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации. Если веб-страница не открывается, щелкните ссылку в окне терминала, чтобы открыть страницу в браузере и войти на портал.

3.	Скачайте файл параметров публикации на компьютер.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Настройка интерфейса командной строки Azure для Windows

Выполните следующие процедуры, чтобы настроить компьютер под управлением Windows для использования интерфейса командной строки Azure:

- Установите интерфейс командной строки Azure (с помощью NPM или установщика Windows)
- Загрузите и импортируйте параметры публикации учетной записи Azure


Интерфейс командной строки Azure можно установить с помощью NPM или установщика Windows. Корпорация Майкрософт рекомендует установку с использованием только одного из двух параметров.

**Чтобы установить интерфейс командной строки Azure с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Нажмите **УСТАНОВИТЬ** и следуйте указаниям, используя параметры по умолчанию.
3.	Откройте **окно командной строки** (или *окно командной строки Azure*, или *окно командной строки разработчика VS2012*) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Если появилось сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменной среды PATH следующих путей: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[имя\_пользователя]\\AppData\\Roaming\\npm</i> или <i>C:\\Program Files\\nodejs;C:\\Users[имя\_пользователя]\\AppData\\Roaming\\npm</i>.

5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Чтобы установить интерфейс командной строки Azure с помощью установщика Windows**

1.	Перейдите в **http://azure.microsoft.com/downloads/**.2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Интерфейс командной строки Azure** и следуйте инструкциям мастера установщика веб-платформы.

**Скачивание и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки Azure, необходимо настроить связь между рабочей станцией и Azure. Информация о подписке Azure используется интерфейсом командной строки Azure для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который будет использоваться интерфейсом командной строки Azure для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [AZURE.NOTE]Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


1.	Откройте **окно командой строки**.
2.	Выполните следующую команду, чтобы скачать файл параметров публикации:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации.

3.	При появлении запроса на сохранение файла щелкните **Сохранить** и укажите место, в котором должен быть сохранен файл.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Подготовка кластеров HDInsight с помощью Azure CLI

Подготовка кластера HDInsight с использованием Azure CLI предусматривает следующие процедуры:

- Создание учетной записи хранения Azure
- Подготовка кластера

**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

- В окне командной строки выполните следующую команду для создания учетной записи хранения Azure:

		azure storage account create [options] <StorageAccountName>

	При появление запроса расположения выберите местоположение, в котором может быть подготовлен кластер HDInsight. Хранилище должно находиться в одном местоположении с кластером HDInsight. В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

Сведения о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Дополнительную информацию о получении данных с использованием портала Azure см. в разделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу* статьи [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Для кластера HDInsight также требуется контейнер в учетной записи хранения. Если в указанной учетной записи хранения еще нет контейнера, то команда *azure hdinsight cluster create* запрашивает у вас имя контейнера, а также создает его. Однако, для создания контейнера заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	! [HDI. CLIClusterCreation] [изображение cli clustercreation]


**Подготовка кластера HDInsight с использованием файла конфигурации**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки Azure предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

- Введите в окне командной строки следующие команды:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Отображение сведений о кластере**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Удаление кластера**

- Используйте следующую команду для удаления кластера:

		azure hdinsight cluster delete <ClusterName>



### Использование пакета SDK HDInsight для .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework.

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Более подробные инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.

5. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.

6. Чтобы установить пакеты, выполните в консоли следующую команду:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. В функции Main() скопируйте и вставьте следующий код:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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

10. Замените переменные в начале функции Main().

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.


##<a id="nextsteps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](hdinsight-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md) — узнайте, как копировать данные между HDInsight и базой данных SQL или сервером SQL Server.
* [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md) — узнайте, как работать с HDInsight, используя PowerShell.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation] — узнайте больше о пакете SDK для HDInsight.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=July15_HO5-->
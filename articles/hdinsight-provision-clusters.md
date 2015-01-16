<properties urlDisplayName="HDInsight Administration" pageTitle="Подготовка кластеров Hadoop в HDInsight для платформы Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Вы узнаете, как подготовить кластеры для Azure HDInsight с помощью портала управления, PowerShell и командной строки." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="jgao" />

#Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров

В этой статье вы познакомитесь с различными способами настраиваемой подготовки кластера Hadoop в Azure HDInsight - с использованием портала управления Azure, PowerShell, средств командной строки или пакета SDК для HDInsight .NET. В статье рассматривается подготовка кластеров Hadoop. Указания по подготовке кластера HBase, см. в разделе [Подготовка кластера HBase в HDInsight][hdinsight-hbase-custom-provision]. См. раздел <a href="http://go.microsoft.com/fwlink/?LinkId=510237">В чем заключается разница между Hadoop и HBase?</a> понять, какой следует сделать выбор.

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, что при упоминании кластеров мы всегда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру ведущий/ведомый с ведущим (также называемым головным узлом или узлом имени) и любым количеством ведомых (также неназываемых узлами данных). Дополнительную информацию см. в разделе [Apache Hadoop][apache-hadoop].

![HDInsight Cluster][img-hdi-cluster]

Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Для получения дополнительной информации см. раздел [Введение для Hadoop в HDInsight][hadoop-hdinsight-intro]. Данные, которые будут перепроверены, располагаются в хранилище больших двоичных объектов Azure, также называемом WASB, в контексте HDInsight. Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

В этой статье приводятся указания по различным способам подготовки кластера. Информацию о быстрой подготовке кластера см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started].

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure - это платформа на основе подписок. Командлеты HDInsight PowerShell предназначены для выполнения задач по подписке. Дополнительную информацию о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##Содержание

* [Настройки конфигурации](#configuration)
* [Использование портала управления Azure](#portal)
* [Использование Azure PowerShell](#powershell)
* [Использование кроссплатформенной командной строки](#cli)
* [Использование пакета SDK для HDInsight .NET](#sdk)
* [Дальнейшие действия](#nextsteps)

##<a id="configuration"></a>Варианты настройки

###Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища BLOB-объектов и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительные BLOB-объекты, которые будут связаны с кластером.

Дополнительную информацию об использовании вторичных хранилищ больших двоичных объектов см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-use-blob-storage/).

###Хранилище мета-данных

Хранилище мета-данных содержит информацию о таблицах Hive, разделах, схемах и т. д. Эта информация используется HIve для определения места расположения информации на HDFS (или WASB на HDInsight). По умолчанию Hive использует встроенную базу данных для хранения подобной информации.

Во время подготовки кластеров HDInsight вы можете задать базу данных SQL, которая будет выполнять роль хранилища метаданных для Hive. Это позволит сохранить метаданные при удалении кластера, поскольку вся информация будет храниться во внешней базе данных SQL.

### Настройка кластера

Вы можете устанавливать дополнительные компоненты или изменять настройку кластера во время создания кластера HDInsight. Настроить кластеры можно с помощью написания сценариев, которые выполняются в процессе создания кластеров. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться из командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в разделе [Customize HDInsight cluster using Script Action][hdinsight-customize-cluster] (Настройка кластера HDInsight с помощью действия сценария).


###Виртуальная сеть

[Виртуальная сеть Azure](http://azure.microsoft.com/ru-ru/documentation/services/virtual-network/) позволяет создать безопасную и стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* Соединить ваши облачные ресурсы с внутренней сетью обработки данных (типа "сервер-сервер" или "клиент-сервер) при помощи Виртуальной частной сети (VPN).

	Конфигурация "сервер-сервер" позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Конфигурация "клиент-сервер" позволит соединить определенные ресурсы с виртуальной сетью Azure при помощи программного обеспечения VPN

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

Дополнительную информацию о характеристиках, преимуществах и возможностях виртуальной сети см. в разделе [Общие сведения о виртуальных сетях Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Дополнительную информацию см. в разделе [Задачи конфигурации виртуальной сети](http://msdn.microsoft.com/ru-ru/library/azure/jj156206.aspx).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

##<a id="portal"></a> Использование портала управления Azure

Кластер HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. Сведения о создании учетной записи хранения Azure см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].


> [WACOM.NOTE] В настоящее время кластеры HDInsight могут размещаться только в регионах **Восточная Азия**, **Юго-восточная Азия**, **Северная Европа**, **Западная Европа **, **Восток США**, **Запад США**, **Северо-центральный регион США** и **Южно-центральный регион США**.

**Чтобы создать кластер HDInsight с использованием возможности настраиваемого создания, выполните следующие действия:**

1. Войдите на [портал управления Azure][azure-management-portal].
2. В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Сведения о кластере** введите или выберите следующие значения:

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

    <table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя кластера</td>
			<td><p>Имя кластера. </p>
				<ul>
				<li>DNS-имя должно начинаться и заканчиваться с буквы или цифры и может содержать дефисы.</li>
				<li>Поле должно представлять собой строку от 3 до 63 символов.</li>
				</ul></td></tr>
		<tr><td>Тип кластера</td>
			<td>Для типа кластера выберите <strong>Hadoop</strong>.</td></tr>
		<tr><td>Версия HDInsight</td>
			<td>Выберите версию. Для Hadoop по умолчанию используется HDInsight версии 3.1, которая использует Hadoop 2.4.</td></tr>
		</table>

	Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4. На странице **Настройка кластера** введите или выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Узлы данных</td><td>Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
	<tr><td>Регион/виртуальная сеть</td><td><p>Выберите тот же регион, что и для учетной записи хранения, созданной в предыдущей процедуре. HDInsight требует размещения учетной записи хранения в том же регионе. При последующей настройке можно выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь.</p><p>Доступны следующие регионы: <strong>Восточная Азия</strong>, <strong>Юго-Восточная Азия</strong>, <strong>Северная Европа</strong>, <strong>Западная Европа</strong>, <strong>Восток США</strong>, <strong>Запад США</strong>, <strong>Северо-центральный регион США</strong>, <strong>Южно-центральный регион США</strong><br/>Если вы создали виртуальную сеть Azure, то можете задать сеть, которую будет использовать кластер HDInsight.</p><p>Дополнительную информацию о создании виртуальной сети Azure см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/azure/jj156206.aspx">Задачи конфигурации виртуальной сети</a>.</p></td></tr>
	</table>


5. На странице **Настройка пользователя кластера** введите следующие значения:

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]

    <table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя пользователя</td>
			<td>Укажите имя пользователя кластера HDInsight.</td></tr>
		<tr><td>Пароль/подтверждение пароля</td>
			<td>Укажите пароль пользователя кластера HDInsight.</td></tr>
		<tr><td>Ввести куст или метахранилище Oozie</td>
			<td>Установите флажок, чтобы указать базу данных SQL, которая будет использоваться в качестве метахранилища Hive/Oozie, в том же центре обработки данных, что и кластер. Это может оказаться полезным, если необходимо сохранить метаданные заданий Hive/Oozie даже после удаления кластера.</td></tr>
		<tr><td>База данных метахранилища</td>
			<td>Укажите базу данных SQL Azure, которая будет использоваться в качестве метахранилища для Hive/OOzie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL в том же центре обработки данных, который указан на странице <strong>Сведения о кластере</strong> .</td></tr>
		<tr><td>Пользователь базы данных</td>
			<td>Укажите пользователя базы данных SQL, от имени которого будет выполняться подключение к базе данных.</td></tr>
		<tr><td>Пароль пользователя базы данных</td>
			<td>Укажите пароль пользователя базы данных SQL.</td></tr>
	</table>

	>[WACOM.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, выберите **Да** и **Сохранить**.

    Щелкните стрелку вправо.


6. На странице **Учетная запись хранения** введите следующие значения:

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Учетная запись хранения</td>
			<td>Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
			<ul>
				<li>Использовать существующее хранилище</li>
				<li>Создать новое хранилище</li>
				<li>Использовать хранилище другой подписки</li>
			</ul>
			</td></tr>
		<tr><td>Имя учетной записи</td>
			<td><ul>
				<li>Если вы хотите использовать существующее хранилище, в поле <strong>Имя учетной записи</strong>выберите существующее имя учетной записи хранения. В раскрывающемся списке указываются только учетные записи хранения, расположенные в том же центре обработки данных, в котором выполняется подготовка кластера.</li>
				<li>Если вы выберите параметр <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище другой подписки</strong> , необходимо указать имя учетной записи хранения.</li>
			</ul></td></tr>
		<tr><td>Ключ учетной записи</td>
			<td>Если вы выберите параметр <strong>Использовать хранилище другой подписки</strong> , укажите ключ учетной записи для соответствующей учетной записи хранения.</td></tr>
		<tr><td>Контейнер по умолчанию</td>
			<td><p>Задает контейнер по умолчанию в учетной записи хранения, который используется в качестве файловой системы по умолчанию для кластера HDInsight. Если вы выберите параметр <strong>Использовать существующее хранилище</strong> для поля <strong>Учетная запись хранения</strong> , и в этой учетной записи нет существующих контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с иным, чем у кластера именем, можно также использовать и этот контейнер.</p>
            <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию</p>
        </td></tr>
		<tr><td>Дополнительные учетные записи хранения</td>
			<td>HDInsight поддерживает несколько учетных записей хранения. Нет ограничений на дополнительную учетную запись хранения, которая может использоваться в кластере. Тем не менее, при создании кластера с использованием портала управления ограничением является семь единиц вследствие ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения к мастеру, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td></tr>
	</table>

	Щелкните стрелку вправо.

7. На странице **Учетная запись хранения** введите сведения о дополнительной учетной записи хранения:

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.

    Щелкните галочку, чтобы начать подготовку кластера. После завершения процесса подготовки в столбце состояния будет отображаться значение  **Работает**.

	> [WACOM.NOTE] После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.

##<a id="powershell"></a> Использование Azure PowerShell
Azure PowerShell - это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этом разделе рассматривается подготовка кластера HDInsight. Информацию о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. Дополнительную информацию об использовании PowerShell с HDInsight см. в разделе [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]. Список командлетов HDInsight PowerShell см. в [справочнике по командлетам HDInsight][hdinsight-powershell-reference].

> [WACOM.NOTE] Сценарии в этом разделе можно использовать для настройки кластеров HDInsight под виртуальную сеть Azure, однако саму виртуальную сеть они создать не смогут. За более подробной информацией о виртуальных сетях Azure обратитесь к разделу [Задачи конфигурации виртуальной сети](http://msdn.microsoft.com/ru-ru/library/azure/jj156206.aspx).

Для подготовки кластера HDInsight с помощью PowerShell необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight.


**Создание учетной записи хранения Azure**

- Выполните следующие команды в окне консоли Azure PowerShell:

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды PowerShell для получения нужных сведений:

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Создание контейнера хранилища больших двоичных объектов Azure**

- Выполните следующие команды в окне консоли Azure PowerShell:

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Подготовка кластера HDInsight:**

> [WACOM.NOTE] Для изменения значения переменных в кластере HDInsight мы советуем использовать только командлеты PowerShell.  Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений.  Значения настроек, заданные с помощью PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне консоли Azure PowerShell:

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	При появлении запроса введите учетные данные для кластера. На подготовку кластера может уйти несколько минут.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации**

При подготовке кластера можно использовать другие параметры конфигурации, такие как подключение к нескольким хранилищам BLOB-объектов Azure, использование виртуальной сети или использование базы данных Azure AQL для хранилищ метаданных Hive и Oozie. Эта позволяет разделить время жизни данных и метаданных от времени жизни кластера.

> [WACOM.NOTE] Для изменения значения переменных в кластере HDInsight мы советуем использовать только командлеты PowerShell.  Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений.  Значения настроек, заданные с помощью PowerShell, будут сохранены при установке исправлений.

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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[WACOM.NOTE] База данных SQL Azure, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, выберите **Да** и **Сохранить**.

**Чтобы отобразить список кластеров HDInsight, выполните следующие действия:**

- Выполните следующие команды в окне консоли Azure PowerShell, чтобы указать кластер HDInsight в списке и убедиться, что он был успешно создан:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Использование кроссплатфомернной командной строки

> [WACOM.NOTE] Начиная с 29.08.2014 кроссплатформенный интерфейс командной строки не может использоваться для "связывания" кластера с виртуальной сетью Azure.

Другим вариантом подготовки кластера HDInsight является интерфейс кроссплатформенной командной строки. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Средство командной строки имеет открытый исходный код.  Управлять исходным кодом можно с помощью GitHub по адресу <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][azure-command-line-tools]. Полную справочную документацию см. в разделе [Средства командной строки Azure для Mac и Linux][azure-command-line-tool]. Эта статья посвящена только интерфейсу командной строки в ОС Windows.

Для подготовки кластера HDInsight с помощью кроссплатформенной командной строки необходимы следующие процедуры:

- Установите кроссплатформенную командную строку
- Загрузите и импортируйте параметры публикации учетной записи Azure
- Создание учетной записи хранения Azure
- Подготовка кластера

Интерфейс командной строки может быть установлен с использованием диспетчера пакетов Node.js (NPM) или установщика Windows. Майкрософт рекомендует проводить установку с использованием только одного из двух параметров.

**Установка интерфейса командной строки с помощью NPM**

1.	Перейдите по адресу **www.nodejs.org**.
2.	Щелкните **УСТАНОВИТЬ** и используйте параметры по умолчанию, следуя указаниям.
3.	Откройте **окно командной строки** (или окно командной строки Azure, или окно командной строки разработчика VS2012) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g azure-cli

	> [WACOM.NOTE] Если вы видите сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменой среды PATH следующих путей: <i>C:\Program Files (x86)\nodejs;C:\Users\[<имя пользователя>]\AppData\Roaming\npm</i> или <i>C:\Program Files\nodejs;C:\Users\[<имя пользователя>]\AppData\Roaming\npm</i>

5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель -h на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Установка интерфейса командной строки с помощью установщика Windows**

1.	Перейдите по адресу **http://azure.microsoft.com/ru-ru/downloads/**.
2.	Прокрутите вниз до раздела **Программы командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте указаниям мастера установщика веб-платформы.

**Скачивание и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [WACOM.NOTE] Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


1.	Откройте **окно командой строки**.
2.	Выполните следующую команду для загрузки файла параметров публикации.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Команда запускает веб-страницу для загрузки с нее файла параметров публикации.

3.	При появлении запроса на сохранение файла щелкните **Сохранить** и укажите расположение, в которое должен быть сохранен файл.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <file>

	На предыдущем снимке файл параметров публикации был сохранен в папку C:\HDInsight на рабочей станции.

**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

- Из окна командной строки выполните следующую команду для создания учетной записи хранения Azure:

		azure storage account create [options] <StorageAccountName>

	При появление запроса на указание места, выберите местоположение, в котором может быть подготовлен кластер HDINsight. Хранилище должно находиться в одном местоположении с кластером HDInsight. В настоящее время кластеры HDInsight могут размещаться только в регионах **Восточная Азия**, **Юго-восточная Азия**, **Северная Европа**, **Западная Европа **, **Восток США**, **Запад США**, **Северо-центральный регион США** и **Южно-центральный регион США**.  

Информацию о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Подробную информацию о получении сведений с использованием портала управления см. в подразделе "Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу" раздела [Создание, управление и удаление учетной записи хранения][azure-create-storageaccount].

Кластер HDInsight также требует наличия контейнера в учетной записи хранения. Если указанная учетная запись хранения еще не имеет контейнер, то команда azure hdinsight cluster create запросит у вас имя контейнера, а также создаст его. Однако, для создания контейнера заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Подготовка кластера HDInsight:**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Чтобы подготовить кластер HDInsight с использованием файла конфигурации, выполните следующие действия:**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

- Введите в окне командной строки следующие команды:


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

	>[WACOM.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, выберите **Да** и **Сохранить**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Чтобы показать информацию о кластере, выполните следующие действия:**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Чтобы удалить кластер, выполните следующие действия:**

- Используйте следующую команду для удаления кластера:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Использование пакета HDInsight .NET SDK
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET.

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Указания будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Указания см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню "Файл" выберите **Создать**, а затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Нажмите кнопку **OK**, чтобы создать проект.

5. В меню **Сервис** щелкните **Диспетчер пакетов Nuget**, затем щелкните **Консоль диспетчера пакетов**.

6. Для установки пакетов выполните следующие команды на консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть этот файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. В функции Main() скопируйте и вставьте следующий код:

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

Когда приложение открыто в Visual Studio, нажмите клавишу **F5**, чтобы запустить его. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.



##<a id="nextsteps"></a> Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started] - узнайте, как начать работу с кластером HDInsight.
* [Использование Sqoop с HDInsight][89e2276a] - узнайте, как копировать данные между HDInsight и базой данных SQL или сервером SQL Server.
* [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell] - узнайте, как работать с HDInsight, используя PowerShell.
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs] - узнайте, как программными средствами отправлять задания в HDInsight.
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation] - узнайте больше о пакете SDK для HDInsight.

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/ru-ru/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /ru-ru/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=35.1-->

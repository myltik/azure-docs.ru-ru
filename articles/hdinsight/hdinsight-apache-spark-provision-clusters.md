<properties 
   pageTitle="Подготовка кластеров Apache Spark в HDInsight | Azure" 
   description="Узнайте, как подготовить кластеры Spark для Azure HDInsight с помощью портала Azure, Azure PowerShell, командной строки или пакета SDK .NET HDInsight" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# Подготовка кластеров Apache Spark в HDInsight с использованием настраиваемых параметров

В большинстве случаев можно подготовить кластер Spark с помощью метода быстрого создания, как описано в разделе [Начало работы с Apache Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). В некоторых сценариях может потребоваться подготовить настраиваемый кластер. Например, может потребоваться подключить внешнее хранилище метаданных для обеспечения постоянных метаданных Hive после истечения времени существования кластера, или может потребоваться дополнительное хранилище для кластера.

Для этих и других сценариев в данной статье приводятся инструкции по использованию портала Azure, Azure PowerShell или пакета SDK .NET HDInsight для подготовки настроенного кластера Spark в HDInsight.


**Предварительные требования:**

Прежде чем переходить к выполнению действий, приведенных в этой статье, необходимо оформить подписку Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Каковы различные параметры конфигурации?

###Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища больших двоичных объектов Azure и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительную учетную запись хранения Azure, которая будет связана с кластером.

>[AZURE.NOTE]Не используйте один контейнер хранилища больших двоичных объектов для нескольких кластеров. Это не поддерживается.

Дополнительную информацию об использовании дополнительных хранилищ больших двоичных объектов см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-use-blob-storage.md).

###Хранилище мета-данных

Spark позволяет определить схему и таблицы Hive в необработанных данных. Эти схемы и метаданные таблицы можно сохранить в метахранилища. Использование метахранилища помогает сохранять метаданные Hive, чтобы не создавать повторно таблицы Hive при подготовке нового кластера. По умолчанию Hive использует встроенную базу данных для хранения подобной информации. Встроенная база данных не сохраняет метаданные при удалении кластера.

### Настройка кластера

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время подготовки. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться посредством портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в разделе [Настройка кластера HDInsight с помощью действия сценария][hdinsight-customize-cluster].


###Виртуальная сеть

[Виртуальная сеть Azure](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть-сеть» или «точка-сеть») при помощи виртуальной частной сети (VPN).

	Конфигурация «сервер-сервер» позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация «точка-сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает виртуальные сети на основе территориальных групп.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

##<a id="portal"></a> Использование портала Azure

Кластеры Spark в HDInsight используют контейнер хранилища больших двоичных объектов Azure как файловую систему по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md). Информацию о создании учетной записи хранения Azure см. в разделе [Как создать учетную запись хранения][azure-create-storageaccount].

**Создание кластера HDInsight с использованием параметра «Настраиваемое создание»**

1. Войдите на [портал Azure][azure-management-portal].
2. В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Сведения о кластере** введите или выберите следующие значения:

	![Указание подробной информации о кластере Spark в HDInsight](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Указание подробной информации о кластере Spark в HDInsight")

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя кластера</td>
		<td><p>Имя кластера. </p>
			<ul>
			<li>DNS-имя должно начинаться и заканчиваться буквой или цифрой и может содержать дефисы.</li>
			<li>Поле должно представлять собой строку от 3 до 63 знаков.</li>
			</ul></td></tr>
	<tr><td>Тип кластера</td>
		<td>Для типа кластера выберите <strong>Spark</strong>.</td></tr>
	<tr><td>Операционная система</td>
		<td>Выберите <b>Windows Server 2012 R2 Data Center</b>, чтобы подготовить кластер Spark, работающий под управлением Windows. </td></tr>
	<tr><td>Версия HDInsight</td>
		<td>Выберите версию. Для Spark единственная доступная версия&#160;— <b>HDInsight 3.2</b>, которая использует Spark 1.3.1.</td></tr>
	</table>
	Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4. На странице **Настройка кластера** введите или выберите следующие значения:

	![Указание количества узлов и типов узлов для кластера](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Указание количества узлов и типов узлов для кластера")

	<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>Узлы данных</td><td>Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
<tr><td>Регион/виртуальная сеть</td><td><p>Выберите тот же регион, что и для учетной записи хранения, созданной в предыдущей процедуре. Для HDInsight требуется, чтобы учетная запись хранения находилась в том же регионе. При последующей настройке можно будет выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь.</p>.<br/>Если вы создали виртуальную сеть Azure, то можете задать сеть, которую будет использовать кластер HDInsight.</p><p>Дополнительную информацию о виртуальных сетях Azure см. в разделе <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Задачи конфигурации виртуальной сети</a>.</p></td></tr>
<tr><td>Размер головного узла</td><td><p>Выберите размер виртуальной машины для головного узла.</p></td></tr>
<tr><td>Размер узла данных</td><td><p>Выберите размер виртуальной машины для узлов данных.</p></td></tr>
</table>
	>[AZURE.NOTE] Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.


5. На странице **Настройка пользователя кластера** введите следующие значения:

    ![Указание пользователя с правами администратора и данных пользователя протокола удаленного рабочего стола](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Указание пользователя с правами администратора и данных пользователя протокола удаленного рабочего стола")

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя пользователя HTTP</td>
		<td>Укажите имя пользователя кластера HDInsight.</td></tr>
	<tr><td>Пароль и подтверждение пароля HTTP</td>
		<td>Укажите пароль пользователя кластера HDInsight.</td></tr>
	<tr><td>Включить удаленный рабочий стол для кластера</td>
		<td>Установите этот флажок, чтобы указать имя, пароль и срок действия для пользователя удаленного рабочего стола, который сможет удаленно подключаться к узлам кластера после его подготовки. Удаленный рабочий стол можно также включить позже, после подготовки кластера. Указания см. в разделе <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.</td></tr>
	<tr><td>Ввести метахранилище Hive или Oozie</td>
		<td>Установите флажок, чтобы указать базу данных SQL, которая будет использоваться в качестве метахранилища Hive или Oozie, в том же центре обработки данных, где расположен кластер. Если флажок установлен, на следующих страницах мастера нужно указать информацию о базе данных SQL Azure. Это может оказаться полезным, если необходимо сохранить метаданные заданий Hive/Oozie даже после удаления кластера. Инструкции по созданию метахранилища см. в разделе <a href="https://azure.microsoft.com/ru-ru/documentation/articles/sql-database-get-started/" target="_blank">Создание первой базы данных SQL Azure</a>.</td></tr>
	</td></tr>
</table>
	>[AZURE.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы** **Windows Azure**, нажмите кнопку **Да**, а затем — **Сохранить**.

    Щелкните стрелку вправо.

6. На странице **Настройка метахранилища Hive или Oozie** введите следующие значения:

    ![Указание сведений о базе данных метахранилища Hive или Oozie](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Указание сведений о базе данных метахранилища Hive или Oozie")

	Укажите базу данных SQL Azure, которая будет использоваться как метахранилище Hive или Oozie. Вы можете указать одну и ту же базу данных для метахранилища Hive и Oozie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL, которые находятся в том же центре обработки данных, который указан на странице <strong>Подробные сведения о кластере</strong>. Укажите также имя пользователя и пароль для подключения к выбранной базе данных SQL Azure.

    >[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

    Щелкните стрелку вправо.

7. На странице **Учетная запись хранения** введите следующие значения:

    ![Указание сведений об учетной записи хранения](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Указание сведений об учетной записи хранения")

	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Учетная запись хранения</td>
		<td>Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
		<ul>
			<li><strong>Использовать существующее хранилище</strong>.</li>
			<li><strong>Создать новое хранилище</strong>.</li>
			<li><strong>Использовать хранилище другой подписки</strong>.</li>
		</ul>
		</td></tr>
	<tr><td>Имя учетной записи</td>
		<td><ul>
			<li>Если используется существующее хранилище, выберите существующую учетную запись хранения для параметра <strong>Имя учетной записи</strong>. В раскрывающемся списке содержатся только те учетные записи хранения, которые расположены в том же центре обработки данных, где выполняется подготовка кластера.</li>
			<li>При выборе параметра <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище из другой подписки</strong> необходимо указать имя учетной записи хранения.</li>
		</ul></td></tr>
	<tr><td>Ключ учетной записи</td>
		<td>Если используется параметр <strong>Использовать учетную запись из другой подписки</strong>, необходимо указать ключ этой учетной записи хранения.</td></tr>
	<tr><td>Контейнер по умолчанию</td>
		<td><p>Задайте контейнер по умолчанию в учетной записи хранения, которая используется в качестве файловой системы по умолчанию для кластера HDInsight. Если вы выберете <strong>Использовать существующее хранилище</strong> в поле <strong>Учетная запись хранения</strong> и в этой учетной записи нет контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с именем, отличающимся от имени кластера, можно также использовать и этот контейнер.</p>
        <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию.</p>
    </td></tr>
	<tr><td>Дополнительные учетные записи хранения</td>
		<td>HDInsight поддерживает несколько учетных записей хранения. Ограничения на дополнительные учетные записи хранения, которые могут использоваться в кластере, отсутствуют. Тем не менее, при создании кластера с использованием портала Azure можно использовать не более семи учетных записей из-за ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения к мастеру, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td></tr>
</table>
	Щелкните стрелку вправо.

8. Если вы решили настроить дополнительное хранилище для кластера, на странице **Учетная запись хранения** введите данные для учетной записи дополнительного хранилища:

	![Указание сведений о дополнительной учетной записи хранения](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Указание сведений о дополнительной учетной записи хранения")

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.

    > [AZURE.NOTE]После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.

8. На странице **Действия сценариев** щелкните **Добавить действие сценария** и укажите информацию о пользовательском сценарии, который необходимо выполнить для настройки кластера при его создании. Дополнительную информацию см. в разделе [Настройка кластеров HDInsight с помощью действия сценария][hdinsight-customize-cluster].

	Если необходимо использовать действие сценария, предоставьте следующие входные данные.
	
	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите URI для сценария, который вызывается для настройки кластера.</td></tr>
	<tr><td>Тип узла</td>
		<td>Укажите узлы, на которых выполняется сценарий настройки. Вы можете выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария.</td></tr>
</table>
	Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов, щелкните флажок, чтобы начать подготовку кластера.



##<a id="powershell"></a>Использование Azure PowerShell

В этом разделе содержатся указания по подготовке кластера Apache Spark в Azure HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell][powershell-install-configure]. Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]. Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight][hdinsight-powershell-reference].

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

- Выполните следующие команды в окне консоли Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE]Указываемые учетные данные используются для создания учетной записи Hadoop в кластере. Эта учетная запись будет использоваться для подключения к кластеру и выполнения заданий. При подготовке кластера с использованием параметра «Быстрое создание» на портале Azure имя пользователя Hadoop по умолчанию — admin. Не путайте эту учетную запись с учетной записью пользователя для подключения к удаленному рабочему столу. Учетная запись пользователя для подключения к удаленному рабочему столу должна отличаться от учетной записи пользователя Hadoop. Дополнительную информацию см. в разделе [Управление кластерами Hadoop в HDInsight с использованием портала управления Azure][hdinsight-admin-portal].

	На подготовку кластера может уйти несколько минут.

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Подготовка кластера Spark с помощью базовой конфигурации в PowerShell")


**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации**

При подготовке кластера можно использовать другие параметры конфигурации, такие как подключение к нескольким контейнерам хранилищ больших двоичных объектов Azure или использование базы данных SQL Azure для метахранилищ Hive и Oozie. Это позволяет разделить срок службы данных и метаданных от срока службы кластера.

> [AZURE.NOTE]Для изменения значения переменных в кластере HDInsight мы советуем использовать только командлеты Windows PowerShell. Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений. Значения настроек, заданные с помощью Azure PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне Windows PowerShell:

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы** **Windows Azure**, нажмите кнопку **Да**, а затем — **Сохранить**.

	На подготовку кластера может уйти несколько минут.

**Отображение списка кластеров HDInsight**

- Выполните следующие команды в окне консоли Azure PowerShell, чтобы указать кластер HDInsight в списке и убедиться, что он был успешно создан:

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a> Использование пакета SDK для HDInsight .NET
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
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. В функции main() скопируйте и вставьте следующий код:

        string thumbprint = "<CertificateFriendlyName>";  
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
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Замените переменные в начале функции main().

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.


##<a id="nextsteps"></a>Дальнейшие действия

* В разделе [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md) представлены справочные сведения о том, как использовать Apache Spark в HDInsight с помощью средств бизнес-аналитики, таких как Power BI и Tableau. 
* В разделе [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md) представлены способы создания приложений машинного обучения с помощью Apache Spark в HDInsight.
* В разделе [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) содержатся сведения о том, как создавать приложения потоковой передачи с помощью Apache Spark в HDInsight.
* В разделе [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md) представлены сведения о том, как использовать диспетчер ресурсов для управления ресурсами, выделенными для кластера Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Использование Sqoop вместе с HDInsight"

<!---HONumber=July15_HO5-->
<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="Администрирование HDInsight" pageTitle="Подготовка кластеров HDInsight | Azure" metaKeywords="hdinsight, администрирование hdinsight, администрирование azure hdinsight" description="Узнайте, как подготовить кластеры Azure HDInsight с помощью портала управления, PowerShell или командной строки." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Подготовка кластеров HDInsight" authors="jgao" />

#Подготовка кластеров HDInsight

В этой статье вы узнаете различные способы подготовки кластера HDInsight.

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure — это платформа на основе подписок. Командлеты HDInsight PowerShell для выполнения задач по подписке. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##В этой статье

* [Использование портала управления Azure](#portal)
* [Использование Azure PowerShell](#powershell)
* [Использование кроссплатфомернной командной строки](#cli)
* [Использование пакета HDInsight .NET SDK](#sdk)
* [Дальнейшие действия](#nextsteps)

##<a id="portal"></a> Использование портала управления Azure

Кластер HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. Сведения о создании учетной записи хранения Azure см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].


> [WACOM.NOTE] В настоящее время только регионы Юго-Восточной Азии, Северной Европы, Западной Европы, а также востока и запада США могут размещать у себя кластеры HDInsight.

На этом занятии описывается процедура по созданию кластера HDInsight с использованием возможности настраиваемого создания.  Сведения об использовании возможности настраиваемого создания см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-getting-started]


**Чтобы создать кластер HDInsight с использованием возможности настраиваемого создания, выполните следующие действия**

1. Выполните вход на [портал управления Azure][azure-management-portal].
2. Щелкните **+ СОЗДАТЬ** в нижней части страницы, щелкните **СЛУЖБЫ ДАННЫХ**, щелкните **HDINSIGHT**, затем щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице "Сведения о кластере" введите или выберите следующие значения:

	<table border='1'>
		<tr><th>Значение</th><th>свойства</th></tr>
		<tr><td>ИМЯ КЛАСТЕРА</td>
			<td><p>Имя кластера. </p>
				<ul>
				<li>DNS-имя должно начинаться и заканчиваться с буквы или цифры и может содержать дефисы.</li>
				<li>Поле должно представлять собой строку от 3 до 63 символов.</li>
				</ul></td></tr>
		<tr><td>УЗЛЫ ДАННЫХ</td>
			<td>Укажите число узлов в кластере. Значение по умолчанию 4.</td></tr>
		<tr><td>ВЕРСИЯ HDINSIGHT</td>
			<td>Выберите версию. Значение по умолчанию — 2.0 при работе кластеров Hadoop 1.2.  При значении 3.0 используются кластеры Hadoop 2.2. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/manage/services/hdinsight/versioning-in-hdinsight/">Какая версия Hadoop включена в Azure HDInsight?</a>.</td></tr>
		<tr><td>Регион</td>
			<td>Укажите центр обработки данных, где установлен кластер. Расположение должно быть таким же, как хранилище BLOB-объектов Azure, которое будет использоваться в качестве файловой системы по умолчанию. В настоящее время можно выбрать только значения *Юго-Восточная Азия*, *Северная Европа*, *Западная Европа*, *Восток США* или *Запад США*.</td>
		</tr>
	</table>

	![HDI.CustomProvision.Page1][image-customprovision-page1]

4. Щелкните стрелку вправо в нижнем правом углу страницы.
5. На странице "Настроить пользователя кластера" введите или выберите следующее значение:

	<table border='1'>
		<tr><th>Значение</th><th>свойства</th></tr>
		<tr><td>ИМЯ ПОЛЬЗОВАТЕЛЯ</td>
			<td>Укажите имя пользователя кластера HDInsight.</td></tr>
		<tr><td><p>ПАРОЛЬ</p><p>ПОДТВЕРДИТЕ ПАРОЛЬ</p></td>
			<td>Укажите пароль пользователя кластера HDInsight.</td></tr>
		<tr><td>Ввести куст или метахранилище Oozie</td>
			<td>Укажите базу данных SQL в том же центре обработки данных, которая будет использоваться в качестве метахранилища Hive/Oozie.</td></tr>
		<tr><td>БАЗА ДАННЫХ HIVE ИЛИ ХРАНИЛИЩА OOZIE</td>
			<td>Укажите базу данных SQL Azure, которая будет использоваться в качестве метахранилища для Hive/OOzie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL в том же центре обработки данных, который указан на странице "Сведения о кластере".</td></tr>
		<tr><td>ПОЛЬЗОВАТЕЛЬ БАЗЫ ДАННЫХ</td>
			<td>Укажите пользователя базы данных SQL, от имени которого будет выполняться подключение к базе данных.</td></tr>
		<tr><td>ПАРОЛЬ ПОЛЬЗОВАТЕЛЯ БАЗЫ ДАННЫХ</td>
			<td>Укажите пароль пользователя базы данных SQL.</td></tr>
	</table>

	Для кластера HDInsight версии 2.0 предоставленные здесь учетные данные позволяют получить доступ только к службам кластера. Удаленный рабочий стол может быть включен после создания кластера.

	![HDI.CustomProvision.Page2][image-customprovision-page2]


6. Щелкните стрелку вправо в нижнем правом углу страницы.
7. На странице учетной записи хранения введите или выберите следующее значение:

	<table border='1'>
		<tr><th>Значение</th><th>свойства</th></tr>
		<tr><td>УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ</td>
			<td>Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
			<ul>
				<li>Использовать существующее хранилище</li>
				<li>Создать новое хранилище</li>
				<li>Использовать хранилище другой подписки</li>
			</ul>
			</td></tr>
		<tr><td>ИМЯ УЧЕТНОЙ ЗАПИСИ</td>
			<td>Если выбран параметр <b>Использовать существующее хранилище</b>, в списке приводятся только учетные записи хранения, расположенные в том же центре обработки данных.</td></tr>
		<tr><td>КЛЮЧ УЧЕТНОЙ ЗАПИСИ</td>
			<td>Это поле доступно только в том случае, если в поле "УЧЕТНАЯ ЗАПИСЬ ХРАНИЛИЩА" выбрано значение <strong>Использовать хранилище другой подписки</strong>.</td></tr>	
		<tr><td>КОНТЕЙНЕР ПО УМОЛЧАНИЮ</td>
			<td>Контейнер по умолчанию в учетной записи хранения будет использован в качестве файловой системы по умолчанию для кластера HDInsight. Если в поле "УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ" выбрано значение <strong>Использовать существующее хранилище</strong> и выбран параметр <strong>Создать контейнер по умолчанию</strong> в поле "КОНТЕЙНЕР ПО УМОЛЧАНИЮ", имя контейнера по умолчанию равно имени кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д.</td></tr>
		<tr><td>ДОПОЛНИТЕЛЬНЫЕ УЧЕТНЫЕ ЗАПИСИ ХРАНЕНИЯ</td>
			<td>HDInsight поддерживает несколько учетных записей хранения. Нет ограничений на дополнительную учетную запись хранения, которая может использоваться в кластере. Тем не менее, при создании кластера с использованием портала управления ограничением является семь единиц вследствие ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 4.</td></tr>		
	</table>

	![HDI.CustomProvision.Page3][image-customprovision-page3]

8. Щелкните стрелку вправо в нижнем правом углу страницы.
9. На странице учетной записи хранения введите сведения о дополнительной учетной записи хранения:

	![HDI.CustomProvision.Page4][image-customprovision-page4]

10. Нажмите кнопку "Завершено" (флажок) в нижнем правом углу, чтобы начать процесс подготовки кластера HDInsight.

На подготовку кластера может уйти несколько минут.  Если процесс подготовки успешно завершен, в столбце состояния кластера будет отображаться **Выполняется**.

> [WACOM.NOTE] После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.













































##<a id="powershell"></a> Использование Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Сведения о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. Дополнительные сведения об использовании PowerShell с HDInsight см. в разделе [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]. Список командлетов HDInsight PowerShell см. в разделе [Справочник командлетов HDInsight][hdinsight-powershell-reference].

Для подготовки кластера HDInsight с помощью PowerShell необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight.


**Создание учетной записи хранения Azure**

- Выполните следующие команды в окне консоли Azure PowerShell:

		$storageAccountName = "<StorageAcccountName>"
		$location = "<MicrosoftDataCenter>"		# For example, "West US"
		
		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
	
	Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды PowerShell для получения нужных сведений:
	
		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"
	
**Создание контейнера хранилища Azure**

- Выполните следующие команды в окне Azure PowerShell:

		$storageAccountName = "<StorageAccountName>"
		$storageAccountKey = "<StorageAccountKey>"
		$containerName="<ContainerName>"

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
		                                       -StorageAccountKey $storageAccountKey  
		 
		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера. 

**Чтобы подготовить кластер HDInsight, выполните следующие действия:**

- Выполните следующие команды в окне Azure PowerShell:		

		$subscriptionName = "<SubscriptionName>"		# The name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# The Azure storage account that hosts the default container. The default container will be used as the default file system.
		$containerName = "<ContainerName>"				# The Azure Blob storage container that will be used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	На подготовку кластера может уйти несколько минут.

	![HDI.CLI.Provision][image-hdi-ps-provision]

Можно также подготовить кластер и настроить его для подключения к нескольким хранилищам BLOB-объектов Azure или настраиваемым метахранилищам Hive и Oozie. Эта расширенная функция позволяет разделить срок службы данных и метаданных от срока службы кластера. 

**Чтобы подготовить кластер HDInsight с использованием конфигурации, выполните следующие действия:**

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
		
		# Get the storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Create a Blob storage container
		#$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		#New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
		
		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location

**Чтобы отобразить список кластеров HDInsight, выполните следующие действия**

- Выполните следующие команды в окне Azure PowerShell:

		Get-AzureHDInsightCluster -Name <ClusterName>











































































































##<a id="cli"></a> Использование кроссплатфомернной командной строки

Другим вариантом подготовки кластера HDInsight является интерфейс кроссплатформенной командной строки. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Средство командной строки имеет открытый исходный код.  Исходный код управляется с помощью GitHub по адресу <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][azure-command-line-tools]. Полную справочную документацию см. в разделе [Средства командной строки Azure для Mac и Linux][azure-command-line-tool]. Эта статья посвящена только интерфейсу командной строки в ОС Windows.


Для подготовки кластера HDInsight с помощью кроссплатформенной командной строки необходимы следующие процедуры:

- Установите кроссплатформенную командную строку
- Загрузите и импортируйте параметры публикации учетной записи Azure
- Создайте учетную запись хранения Azure
- Подготовьте кластер

Интерфейс командной строки может быть установлен с использованием *диспетчера пакетов Node.js (NPM)* или установщика Windows.

**Чтобы установить интерфейс командной строки с помощью NPM, выполните следующие действия**

1.	Перейдите к **www.nodejs.org**
2.	Щелкните **УСТАНОВИТЬ** и выполните инструкции с использованием параметров по умолчанию.
3.	Откройте **окно командной строки** (или *окно командной строки Azure*, или *окно командной строки разработчика VS2012*) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g azure-cli

	> [WACOM.NOTE] Если возникло сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменой среды PATH следующих путей: <i>C:\Program Files (x86)\nodejs;C:\Users\[имя_пользователя]\AppData\Roaming\npm</i> или <i>C:\Program Files\nodejs;C:\Users\[имя_пользователя]\AppData\Roaming\npm</i>
	


5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации.  Например:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Чтобы установить интерфейс командной строки с помощью установщика windows, выполните следующие действия**

1.	Перейдите по адресу **http://www.windowsazure.com/ru-ru/downloads/**.
2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте инструкциям мастера установщика веб-платформы.

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.


> [WACOM.NOTE] Файл параметров публикации содержит конфиденциальные сведения. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


**Загрузка и импорт параметров публикации**

1.	Откройте **окно командой строки**.
2.	Выполните следующую команду для загрузки файла параметров публикации.

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Команда отображает инструкции по загрузке файла, включая URL-адрес.

3.	Откройте **Internet Explorer** и перейдите к URL-адресу указанному в окне командой строки.
4.	Нажмите кнопку **Сохранить** для сохранения файла на рабочей станции.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <file>

	На предыдущем снимке файл параметров публикации был сохранен в папку C:\HDInsight на рабочей станции.


HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

**Создание учетной записи хранения Azure**

- Введите в окне командной строки следующую команду:

		azure account storage create [options] <StorageAccountName>

Сведения о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

Подробные сведения о получении сведений с использованием портала управления см. в разделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа* статьи [Управление учетными записями хранения][azure-manage-storageaccount].

Команда *azure hdinsight cluster create* создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Чтобы создать кластер HDInsight, выполните следующие действия**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]

















Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.  

**Чтобы подготовить кластер HDInsight с использованием файла конфигурации, выполните следующие действия:**

- Введите в окне командной строки следующую команду:
 
		azure hdinsight cluster config create <file>
		 
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
		 
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"
		 
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
		 
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
		 
		azure hdinsight cluster create --config <file>
		 


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Чтобы показать сведения о кластере, выполните следующие действия**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>
	
	![HDI.CLIListCluster][image-cli-clusterlisting]


**Чтобы удалить кластер, выполните следующие действия**

- Используйте следующую команду для удаления кластера.

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Использование пакета HDInsight .NET SDK
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с HDInsight в .NET. 

Для подготовки кластера HDInsight с помощью пакета SDK необходимы следующие процедуры:

- Установите пакет HDInsight .NET SDK
- Создайте консольное приложение
- Выполните приложение


**Установка пакета HDInsight .NET SDK**
Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2012.

2. В меню "Файл" щелкните **Создать**, затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

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


5. В меню **Сервис** щелкните **Диспетчер пакетов библиотеки**, щелкните **Консоль диспетчера пакетов**.

6. Выполните следующие команды в консоли, чтобы установить пакеты.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть этот файл.

8. Добавьте в начало файла следующие инструкции:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	
9. Скопируйте и вставьте в функцию Main() следующий код:
		
        string certfriendlyname = "<CertificateFriendlyName>";
        string subscriptionid = "<WindowsAzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
        string storageaccountkey = "<WindowsAzureStorageAccountKey>";
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



##<a id="nextsteps"></a> Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-getting-started]
* [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Документация по пакету Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-version]: /ru-ru/manage/services/hdinsight/versioning-in-hdinsight/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
[hdinsight-getting-started]: /ru-ru/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /ru-ru/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /ru-ru/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-submit-jobs]: /ru-ru/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-configure-powershell]: /ru-ru/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-powershell-reference]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx

[azure-create-storageaccount]: /ru-ru/manage/services/storage/how-to-create-a-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/
[azure-command-line-tools]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
[azure-command-line-tool]: /ru-ru/manage/linux/other-resources/command-line-tools/
[azure-manage-storageaccount]: /ru-ru/manage/services/storage/how-to-manage-a-storage-account/
[azure-purchase-options]: https://www.windowsazure.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ru-ru/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ru-ru/pricing/free-trial/

[Powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/


[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png 
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png 
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png 
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png 

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png



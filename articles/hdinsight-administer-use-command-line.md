<properties 
	pageTitle="Управление кластерами Hadoop с помощью кроссплатформенного интерфейса командной строки | Azure" 
	description="Узнайте, как использовать кроссплатформенный интерфейс командной строки для управления кластерами Hadoop в HDIsight на любой платформе, поддерживающей Node.js, включая Windows, Mac и Linux.." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с использованием межплатформенного интерфейса командной строки

В этой статье вы узнаете, как использовать межплатформенный интерфейс командной строки для управления кластерами Hadoop в HDInsight. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. 

Средство командной строки имеет открытый исходный код.  Управлять исходным кодом можно с помощью GitHub по адресу <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>. 

Эта статья посвящена только интерфейсу командной строки в ОС Windows. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][azure-command-line-tools]. Полную справочную документацию см. в разделе [Средства командной строки Windows Azure для Mac и Linux][azure-command-line-tool].


**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure**. Azure - это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##Содержание

* [Установка](#installation)
* [Скачивание и импорт файла publishsettings учетной записи Azure](#importsettings)
* [Подготовка кластера к работе](#provision)
* [Подготовка кластера с использованием файла конфигурации](#provisionconfigfile)
* [Отображение кластеров](#listshow)
* [Удаление кластера](#delete)
* [Дальнейшие действия](#nextsteps)

##<a id="installation"></a> Установка
Интерфейс командной строки можно установить, используя *Node.js Package Manager (NPM)* или установщик Windows.

**Установка интерфейса командной строки с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Щелкните **УСТАНОВИТЬ** и выполните инструкции с использованием параметров по умолчанию.
3.	Откройте **окно командой строки** (или *Azure Command Prompt*, или *Developer Command Prompt for VS2012*) на рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g azure-cli

	> [AZURE.NOTE] Если возникло сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменой среды PATH следующих путей: <i>C:\Program Files (x86)\nodejs;C:\Users\[имя пользователя]\AppData\Roaming\npm</i> или <i>C:\Program Files\nodejs;C:\Users\[имя пользователя]\AppData\Roaming\npm</i>


5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации.  Например:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Установка интерфейса командной строки с помощью установщика Windows**

1.	Перейдите к **http://azure.microsoft.com/ru-ru/downloads/**.
2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте инструкциям мастера установщика веб-платформы.

##<a id="importsettings"></a> Скачивание и импорт файла publishsettings учетной записи Azure

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить из файла publishsettings через портал Azure. Файл publishsettings можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать файл publishsettings только один раз.

> [AZURE.NOTE] Файл publishsettings содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


**Скачивание и импорт файла publishsettings**

1.	Откройте **окно командой строки**.
2.	Выполните следующую команду, чтобы загрузить файл publishsettings:

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Команда отображает инструкции по загрузке файла, включая URL-адрес.

3.	Откройте **Internet Explorer** и перейдите к URL-адресу указанному в окне командой строки.
4.	Нажмите кнопку **Сохранить** для сохранения файла на рабочей станции.
5.	В окне командной строки выполните следующую команду, чтобы импортировать файл publishsettings:

		azure account import <file>

	На предыдущем снимке файл publishsettings был сохранен в папку C:\HDInsight на рабочей станции.


##<a id="provision"></a> Подготовка кластера HDInsight

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure. 

После импорта файла publishsettings используйте следующую команду для создания учетной записи хранения:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE] Учетная запись хранения должна находиться в том же центре обработки данных. В настоящее время можно подготовить только кластеры HDInsight в следующих центрах обработки данных:

><ul>
<li>Юго-Восточная Азия</li>
<li>Северная Европа</li>
<li>Западная Европа</li>
<li>Восток США</li>
<li>Запад США</li>
</ul>


Информацию о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание и удаление учетной записи хранения, а также управление ей][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

Подробную информацию о получении сведений с использованием портала управления см. в подразделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу* раздела [Создание и удаление учетной записи хранения, а также управление ей][azure-create-storageaccount].


Команда *azure hdinsight cluster create* создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера. 

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##<a id="provisionconfigfile"></a> Подготовка кластера HDInsight с использованием файла конфигурации
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
		 


![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##<a id="listshow"></a> Отображение сведений о кластере
Используйте следующие команды для отображения сведений о кластере:
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##<a id="delete"></a> Удаление кластера
Используйте следующую команду для удаления кластера:

	azure hdinsight cluster delete <ClusterName>




##<a id="nextsteps"></a> Дальнейшие действия
В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Администрирование HDInsight с использованием портала управления][hdinsight-admin-portal]
* [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Использование программ командной строки Azure для Mac и Linux][azure-command-line-tools]
* [Программа командной строки Azure для Mac и Linux][azure-command-line-tool]


[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/


[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png 
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"
<!--HONumber=42-->

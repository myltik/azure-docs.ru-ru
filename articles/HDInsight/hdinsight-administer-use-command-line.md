<properties 
	pageTitle="Управление кластерами Hadoop с помощью интерфейса командной строки | Microsoft Azure" 
	description="Узнайте, как использовать кроссплатформенный интерфейс командной строки для управления кластерами Hadoop в HDIsight на любой платформе, поддерживающей Node.js, включая Windows, Mac и Linux." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с использованием кроссплатформенного интерфейса командной строки

Узнайте, как использовать интерфейс командной строки Azure для Mac, Linux и Windows для управления кластерами Hadoop в Azure HDInsight. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux.

Azure CLI имеет открытый исходный код. Этот исходный код управляется в GitHub по адресу <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

В этой статье рассматривается только интерфейс командной строки в Windows. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][azure-command-line-tools].


##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure** — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##Установка
Интерфейс командной строки может быть установлен с помощью *диспетчера пакетов Node.js (NPM)* или установщика Windows.

**Установка интерфейса командной строки с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Нажмите **УСТАНОВИТЬ** и следуйте указаниям, используя параметры по умолчанию.
3.	Откройте **окно командной строки** (или **окно командной строки Azure**, или **окно командной строки разработчика VS2012**) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g azure-cli

	> [AZURE.NOTE]Если появилось сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменной среды **PATH** следующих путей: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[имя_пользователя]\\AppData\\Roaming\\npm</i> или <i>C:\\Program Files\\nodejs;C:\\Users[имя_пользователя]\\AppData\\Roaming\\npm</i>.


5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель **-h** на различных уровнях для отображения справочной информации. Например:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Установка интерфейса командной строки с помощью установщика Windows**

1.	Перейдите в **http://azure.microsoft.com/downloads/**.
2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте инструкциям мастера установщика веб-платформы.

##Загрузка и импорт файла publishsettings учетной записи Azure

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить из файла publishsettings через портал Azure. Файл publishsettings можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Файл publishsettings нужно импортировать только один раз.

> [AZURE.NOTE]Файл publishsettings содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. В Windows измените свойства папки или используйте шифрование дисков BitLocker.


**Загрузка и импорт файла publishsettings**

1.	Откройте окно командной строки.
2.	Выполните следующую команду, чтобы загрузить файл publishsettings:

		azure account download
 
	![Интерфейс командной строки, загрузка учетной записи Azure.][image-cli-account-download-import]

	Эта команда отображает инструкции по загрузке файла, включая URL-адрес.

3.	Откройте Internet Explorer и перейдите к URL-адресу, указанному в окне командой строки.
4.	Нажмите кнопку **Сохранить** для сохранения файла на рабочей станции.
5.	В окне командной строки выполните следующую команду, чтобы импортировать файл publishsettings:

		azure account import <file>

	На предыдущем снимке файл publishsettings был сохранен в папку C:\\HDInsight на рабочей станции.


##Подготовка кластера HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure.

После импорта файла publishsettings используйте следующую команду для создания учетной записи хранения:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Учетная запись хранения должна находиться вместе с HDInsight в том же центре обработки данных.


Сведения о создании учетной записи хранения Azure на портале Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Дополнительные сведения о получении данных с помощью портала Azure см. в разделе «Просмотр, копирование и повторное создание ключей доступа к хранилищу» статьи [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].


Команда **azure hdinsight cluster create** создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Подготовка кластера HDInsight с помощью файла конфигурации
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


##Отображение сведений о кластере
Используйте следующие команды для отображения сведений о кластере:
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##Удаление кластера
Используйте следующую команду для удаления кластера:

	azure hdinsight cluster delete <ClusterName>




##Дальнейшие действия
В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
* [Начало работы с Azure HDInsight][hdinsight-get-started]
* [Использование интерфейса командной строки Azure для Mac, Linux и Windows][azure-command-line-tools].


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Отображение кластеров"

<!--HONumber=54--> 
<properties
	pageTitle="Управление кластерами Hadoop с помощью CLI Azure | Microsoft Azure"
	description="Использование CLI Azure для управления кластерами Hadoop в HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью интерфейса командной строки (CLI) Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Узнайте, как использовать [интерфейс командной строки Azure](xplat-cli-install.md) для управления кластерами Hadoop в Azure HDInsight. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux.

Azure CLI имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

В этой статье описывается только использование CLI Azure с HDInsight. Общее руководство по использованию CLI Azure см. в статье [Установка и настройки CLI Azure][azure-command-line-tools].


##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI Azure** — Сведения об установке и настройке CLI Azure см. в разделе [Установка и настройка CLI Azure](../xplat-cli-install.md) .
- **Подключитесь к Azure**, выполнив следующую команду:

		azure login

	Дополнительную информацию об аутентификации с помощью рабочей или учебной учетной записи см. в разделе [Подключение к подписке Azure через интерфейс командной строки Azure](xplat-cli-connect.md).
	
- **Переключитесь в режим диспетчера ресурсов Azure** с помощью следующей команды:

		azure config mode arm


##Создание кластеров

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

Перед созданием кластера HDInsight необходимо иметь службу управления ресурсов Azure (ARM) и учетную запись хранилища больших двоичных объектов Azure. Чтобы создать кластер HDInsight, необходимо указать следующее:

- **Группа ресурсов Azure**: необходимо создать учетную запись аналитики озера данных в группе "Ресурс Azure". Диспетчер ресурсов Azure позволяет вам работать с ресурсами в приложении в виде группы. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции. 

	Для перечисления групп ресурсов в своей подписке выполните следующие действия.
	
		azure group list 
	
	Чтобы создать новую группу ресурсов:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Имя кластера HDInsight.**

- **Расположение**. Один из центров обработки данных Azure, который поддерживает кластеры HDInsight. Список поддерживаемых кластеров см. в разделе [Цены на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Учетная запись хранения по умолчанию**. Кластер HDInsight использует контейнер хранилища больших двоичных объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure.

	Создание новой учетной записи хранения Azure
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]Учетная запись хранения должна находиться вместе с HDInsight в том же центре обработки данных. Тип учетной записи хранилища не может быть ZRS, так как ZRS не поддерживает таблицы.

	Сведения о создании новой учетной записи хранения Azure с помощью портала предварительной версии Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].
	
	Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Дополнительные сведения о получении данных с помощью портала предварительной версии Azure см. в разделе "Просмотр, копирование и повторное создание ключей доступа к хранилищу" статьи [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].

- **(Необязательно) Контейнер больших двоичных объектов по умолчанию**. Команда **azure hdinsight cluster create** создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:

	azure storage container create —имя\_учетной\_записи "<Storage Account Name>" —ключ\_учетной\_записи <StorageAccountKey> [Имя\_контейнера]

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<Storage Account Name>" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Создание кластеров с помощью файлов конфигурации
Как правило, необходимо создать кластер HDInsight, выполнить в нем задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждом создании кластера.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName ""<Storage Account Name>".blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName ""<Storage Account Name>".blob.core.windows.net"
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


##Удаление кластеров
Используйте следующую команду для удаления кластера:

	azure hdinsight cluster delete <ClusterName>

##Масштабирование кластеров

Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>

##Дальнейшие действия
В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Администрирование HDInsight с помощью портала предварительной версии Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Использование CLI Azure][azure-command-line-tools]


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

<!---HONumber=Nov15_HO2-->
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
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с использованием интерфейса командной строки Azure (CLI Azure)

[AZURE.INCLUDE [селектор](../../includes/hdinsight-portal-management-selector.md)]

Узнайте, как использовать CLI Azure для управления кластерами Hadoop в Azure HDInsight. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux.

Azure CLI имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

В этой статье описывается только использование CLI Azure с HDInsight. Общее руководство по использованию CLI Azure см. в статье [Использование CLI Azure][azure-command-line-tools].


##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **CLI Azure** — Сведения об установке и настройке CLI Azure см. в разделе [Установка и настройка CLI Azure](../xplat-cli.md) .

##Установка

Если вы этого еще не сделали, используйте документ [Установка и настройка CLI Azure](../xplat-cli.md) для установки и настройки CLI Azure.

##Подготовка кластера HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure.

После импорта файла publishsettings используйте следующую команду для создания учетной записи хранения:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Учетная запись хранения должна находиться вместе с HDInsight в том же центре обработки данных.


Сведения о создании новой учетной записи хранения Azure с помощью портала предварительной версии Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Дополнительные сведения о получении данных с помощью портала предварительной версии Azure см. в разделе «Просмотр, копирование и повторное создание ключей доступа к хранилищу» статьи [Создание и удаление учетной записи хранения, а также управление ею][azure-create-storageaccount].


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

<!---HONumber=August15_HO8-->
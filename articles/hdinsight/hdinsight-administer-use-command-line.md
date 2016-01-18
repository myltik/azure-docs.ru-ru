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
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью интерфейса командной строки (CLI) Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Узнайте, как использовать [интерфейс командной строки Azure](xplat-cli-install.md) для управления кластерами Hadoop в Azure HDInsight. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux.

В этой статье описывается только использование CLI Azure с HDInsight. Общее руководство по использованию Azure CLI см. в статье [Установка и настройка Azure CLI][azure-command-line-tools].

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI Azure** — Сведения об установке и настройке CLI Azure см. в разделе [Установка и настройка CLI Azure](../xplat-cli-install.md) .
- **Подключитесь к Azure**, выполнив следующую команду.

		azure login

	Дополнительную информацию об аутентификации с помощью рабочей или учебной учетной записи см. в разделе [Подключение к подписке Azure через интерфейс командной строки Azure](xplat-cli-connect.md).
	
- **Переключитесь в режим диспетчера ресурсов Azure** с помощью следующей команды.

		azure config mode arm

Чтобы получить справку, используйте параметр **-h**. Например:

	azure hdinsight cluster create -h
	
##Создание кластеров

См. раздел [Создание кластеров Hadoop под управлением Windows в HDInsight с помощью интерфейса командной строки Azure](hdinsight-hadoop-create-windows-clusters-cli.md).

##Отображение сведений о кластере
Используйте следующие команды для отображения сведений о кластере:

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Удаление кластеров
Используйте следующую команду для удаления кластера:

	azure hdinsight cluster delete <Cluster Name>

##Масштабирование кластеров

Изменение размера кластера Hadoop

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Включение или отключение доступа по протоколу HTTP для кластера

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Включение или отключение доступа по протоколу RDP для кластера

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Дальнейшие действия
В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
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

<!---HONumber=AcomDC_0107_2016-->
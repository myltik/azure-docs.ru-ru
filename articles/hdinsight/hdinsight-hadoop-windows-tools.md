---
title: Использование Hadoop в Azure HDInsight на компьютере с Windows | Документация Майкрософт
description: Сведения об использовании Hadoop в HDInsight на компьютере с Windows, а также о запросе кластеров и управлении ими с помощью PowerShell, Visual Studio и средств Linux. Разработка решений для работы с большими данными на языке .NET.
services: hdinsight
keywords: hadoop в windows, hadoop для windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 2067e36c931e62e25d0c8dca69a6a81b2afe3f87
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404989"
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Работа в экосистеме Hadoop в HDInsight на компьютере с Windows

В этой статье приведены сведения о способах развертывания экосистемы Hadoop в HDInsight и управления ею при работе на компьютере с Windows. 

Служба HDInsight включает в себя компоненты Apache Hadoop и Hadoop, технологии с открытым исходным кодом, разработанные на платформе Linux. HDInsight 3.4 и более поздних версий использует дистрибутив Ubuntu Linux в качестве базовой операционной системы кластера. Но с HDInsight можно также работать в клиенте или среде разработки Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Задачи развертывания и управления с помощью PowerShell
Azure PowerShell — это среда сценариев, которая позволяет контролировать и автоматизировать задачи развертывания и управления в HDInsight на компьютере с Windows.

При помощи PowerShell можно выполнять такие задачи:

* [создавать кластеры](hdinsight-hadoop-create-linux-clusters-azure-powershell.md);
* [Выполнение запросов Hive с помощью PowerShell](hadoop/apache-hadoop-use-hive-powershell.md)
* [управлять кластерами](hdinsight-administer-use-powershell.md).

Указания по установке и настройке последней версии Azure PowerShell см. [здесь](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Если у вас есть сценарии, в которые нужно добавить новые командлеты для Azure Resource Manager, см. статью [Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Браузерные служебные программы
Ниже приведены примеры служебных программ с веб-интерфейсом, выполняемым в браузере.
* **[Azure Cloud Shell (предварительная версия)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  — интерактивная оболочка командной строки, выполняемая в браузере и на портале Azure.
* **[Веб-интерфейс Ambari](hdinsight-hadoop-manage-ambari.md)**  — служебная программа для управления и мониторинга, доступная на портале Azure. Она позволяет управлять разными видами заданий, например:
    * [интеграция Ambari с REST API](hdinsight-hadoop-manage-ambari-rest-api.md);
    * [используйте представление Hive в Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [использование представлений Tez в Ambari](hdinsight-debug-ambari-tez-view.md).

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Средства Data Lake (Hadoop) для Visual Studio
Средства Data Lake для Visual Studio позволяют развертывать топологии Storm и управлять ими. Эти средства также устанавливают пакет SDK для SCP.NET, который позволяет разрабатывать топологии Storm на языке C# с помощью Visual Studio.

Перед выполнением приведенных ниже примеров [установите средства Data Lake для Visual Studio и попробуйте работать с ними](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

При помощи средств Data Lake и Visual Studio можно выполнять следующие задачи:
* [Развертывать топологии Storm и управлять ими](storm/apache-storm-deploy-monitor-topology-linux.md).
* [Разрабатывать топологии Storm на языке C#](storm/apache-storm-develop-csharp-visual-studio-topology.md). Биты содержат примеры шаблонов топологий Storm, которые можно подключать к базам данных, таким как Azure Cosmos DB и база данных SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio и пакет SDK для .NET 

Visual Studio с пакетом SDK для .NET позволяет управлять кластерами и разрабатывать приложения больших данных. Ниже приведены примеры задач, которые можно выполнять в Visual Studio. Но их также можно выполнять и с помощью других интерфейсов IDE.

При помощи пакета SDK для .NET в Visual Studio можно выполнять следующие задачи:
* [создавать кластеры и работать в HDInsight из приложения .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md);
* [выполнять запросы Hive с помощью пакета SDK для .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md);
* [использовать определяемые пользователем функции C# при потоковой передаче Hive и Pig в Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

> Совет. Если вы используете решения .NET с кластерами HDInsight под управлением Windows, мы советуем перенести эти решения в кластеры под управлением Linux. Дополнительные сведения см. в статье [Перенос решений .NET из HDInsight под управлением Windows в HDInsight под управлением Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA и Eclipse IDE для кластеров Spark
При помощи [Intellij IDEA](https://www.jetbrains.com/idea/download) и [Eclipse IDE](https://www.eclipse.org/downloads/) можно выполнять следующие задачи:
* разрабатывать и отправлять приложения Scala Spark в кластер HDInsight Spark;
* получать доступ к ресурсам кластера Spark;
* разрабатывать и запускать приложения Scala Spark в локальной среде.

В этих статьях описывается: 
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](spark/apache-spark-intellij-tool-plugin.md) (Intellij IDEA).
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для Eclipse](spark/apache-spark-eclipse-tool-plugin.md) (Eclipse IDE или Scala IDE для Eclipse). 


## <a name="notebooks-on-spark-for-data-scientists"></a>Записные книжки в Spark для специалистов по обработке и анализу данных 
Кластеры Apache Spark в HDInsight включают записные книжки Zeppelin и ядра, которые можно использовать с записными книжками Jupyter. 

* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Запуск средств и технологии Linux в Windows

Если возникнет ситуация, когда необходимо использовать средства или технологии, доступные только в Linux, рассмотрите следующие варианты:

* **Bash (бета-версия) на платформе Windows 10** предоставляет подсистему Linux в Windows. Это средство позволяет напрямую запускать служебные программы Linux, не устанавливая выделенный сервер Linux. [Установите и запустите бета-версию средства Bash на платформе Windows 10.](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker для Windows** предоставляет доступ ко многим средствам Linux. Эту платформу можно запустить непосредственно из Windows. Например, с помощью Docker можно запустить клиент Beeline для Hive непосредственно из Windows. Кроме того, Docker позволяет запускать локальные записные книжки Jupyter и удаленно подключаться к Spark в HDInsight. [Начните работу с Docker для Windows.](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)** позволяет графически просматривать файловую систему кластера через SSH-подключение.

## <a name="next-steps"></a>Дополнительная информация
Если вы только начали работать с кластерами под управлением Linux, см. сведения в следующих статьях:
* [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md)

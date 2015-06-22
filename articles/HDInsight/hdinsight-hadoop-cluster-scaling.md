<properties
   pageTitle="Масштабирование кластера в HDInsight | Azure"
   description="Измените количество узлов данных кластера, работающего в HDInsight, без необходимости удалять и повторно создавать кластер."
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
   ms.date="04/02/2015"
   ms.author="jgao"/>

#Масштабирование кластера в HDInsight

Масштабирование кластера позволяет вам изменить число узлов данных в кластере, который работает в Azure HDInsight. При этом не требуется удалять и повторно создавать кластер. Эту операцию можно выполнить с использованием Azure PowerShelll, пакета SDK для HDInsight или портала Azure.

## Обзор функции
В этом разделе описывается влияние изменения количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Добавление узлов данных
Вы можете с легкостью увеличить количество узлов данных в кластере Hadoop, который работает, не влияя на ожидающие и выполняющиеся задания. В ходе выполнения операции можно также отправлять новые задания. Сбои операции масштабирования обрабатываются корректно, поэтому кластер всегда пребывает в функциональном состоянии.

### Удаление узлов данных
Если уменьшить масштаб кластера Hadoop, сократив количество узлов данных, некоторые службы в нем будут перезапущены. Это приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.

## Storm
Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере Storm. Но после успешного завершения операции масштабирования потребуется повторная балансировка топологии.

Повторную балансировку можно выполнить двумя способами:

* с помощью веб-интерфейса Storm;
* с помощью программы командной строки. 

Дополнительную информацию см. в [документации по Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

В кластере HDInsight доступен веб-интерфейс Storm.

![рисунок 1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Ниже приведен пример использования команды CLI для повторной балансировки топологии Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере HBase. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Но их также можно сбалансировать вручную, выполнив вход в головной узел кластера и выполнив следующие команды в окне командной строки:

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## Предварительные требования

* Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, ее можно проверить на портале Azure, щелкнув имя кластера HDInsight или выполнив команду `Get-AzureHDInsightCluster –name <clustername>` из Azure PowerShell.

* Для выполнения операции с помощью Azure PowerShell требуется Azure PowerShell версии 0.8.14 или более поздней. Последнюю версию Azure PowerShell можно скачать в разделе **программ командной строки** на веб-сайте [Загрузки Azure](http://azure.microsoft.com/downloads/). Вы можете проверить номер установленной версии Azure PowerShell, выполнив следующую команду в окне Azure PowerShell: `(get-module Azure).Version`.

## Как использовать масштабирование кластера

### Портал Azure
Размер работающего кластера можно изменить на вкладке **МАСШТАБИРОВАНИЕ** панели мониторинга кластера Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]Для использования этой команды на клиентском компьютере необходимо установить Azure PowerShell версии 0.8.14 или более поздней.

### SDK
Чтобы изменить размер кластера Hadoop с помощью пакета SDK для HDInsight, воспользуйтесь одним из следующих методов:

	ChangeClusterSize(string dnsName, string location, int newSize) 

или

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Синхронные и асинхронные версии этого метода возвращают объект [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

Ниже приведен пример кода, в котором показано, как использовать синхронную версию этого метода:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Дополнительную информацию об использовании пакета SDK для HDInsight .NET см. в статье [Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров](hdinsight-provision-clusters.md).

<!--HONumber=54--> 
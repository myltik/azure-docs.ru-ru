<properties 
	pageTitle="Программный доступ к журналам приложений Hadoop YARN в HDInsight | Microsoft Azure" 
	description="Программный доступ к журналам приложений в кластере Hadoop в HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Программный доступ к журналам приложений YARN в Hadoop в HDInsight

В этом разделе объясняется, как программно перечислить приложения YARN (Yet Another Resource Negotiator), которые завершены в кластере Hadoop в Azure HDInsight, и как получить программный доступ к журналам приложений без необходимости подключения к кластерам по протоколу удаленного рабочего стола (RDP). В частности, добавлены новый компонент и новый интерфейс API.

  1. В кластерах HDInsight включен универсальный сервер журнала приложений. Это компонент сервера временной шкалы YARN, отвечающий за хранение и извлечение общей информации о завершенных приложениях.
  2. В пакет SDK для Azure для HDInsight .NET добавлены новые API, которые позволяют программно перечислять приложения, запущенные в кластерах, и скачивать журналы соответствующего приложения или контейнера (в виде обычного текста), что упрощает отладку приложений.


## Предварительные требования

Пакет SDK для Azure HDInsight должен использовать код, представленный в этом разделе в приложении .NET Framework. Последняя опубликованная сборка пакета SDK доступна на сайте [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started).

Чтобы установить пакет SDK для HDInsight в приложении Visual Studio, откройте меню **Инструменты**, щелкните **Диспетчер пакетов NuGet** и выберите **Консоль диспетчера пакетов**. Чтобы установить пакеты, выполните в консоли следующую команду:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Эта команда добавляет библиотеки .NET для HDInsight и ссылки на них в текущий проект Visual Studio.


## <a name="YARNTimelineServer"></a>YARN Timeline Server

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> предоставляет общую информацию о завершенных приложениях, а также данные о приложениях для конкретной платформы в двух различных интерфейсах. В частности:

* Возможность хранения и извлечения общей информации о приложениях в кластерах HDInsight появилась, начиная с версии 3.1.1.374. 
* В настоящее время компонент сервера временной шкалы, предоставляющий сведения о приложениях для конкретной платформы, недоступен в кластерах HDInsight.


К общим сведениям о приложениях относятся следующие типы данных:

* уникальный идентификатор приложения; 
* имя пользователя, запустившего приложение; 
* информация о попытках завершить приложение; 
* информация о контейнерах, которые использовались во время каждой из попыток. 

В кластерах HDInsight эта информация сохраняется диспетчером ресурсов Azure в хранилище журналов в контейнере учетной записи хранения по умолчанию. Эти общие данные о завершенных приложениях можно извлечь с помощью REST API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Мы добавили новые API в пакет SDK для HDInsight .NET, чтобы упростить процесс программного извлечения этих данных. Обратите внимание, что общие данные также можно извлечь, выполнив команды интерфейса командной строки YARN непосредственно в узлах кластера (после подключения к кластеру по протоколу RDP).

## <a name="YARNAppsAndLogs"></a>Приложения и журналы YARN

YARN поддерживает несколько моделей программирования (в том числе MapReduce), отделяя управление ресурсами от планирования и мониторинга приложений. Это осуществляется с помощью глобального *диспетчера ресурсов*, *диспетчеров узлов* на каждый рабочий узел и *диспетчеров приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Кроме того, для выполнения приложения при наличии сбоев или из-за потери связи между диспетчером приложений и диспетчером ресурсов может потребоваться несколько *попыток*. Таким образом, контейнеры предоставляются определенной попытке. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. Дополнительную информацию см. в статье об [основных понятиях YARN][YARN-concepts].

Журналы приложений (и соответствующие журналы контейнеров) крайне важны для отладки проблемных приложений Hadoop. YARN предоставляет хорошую платформу для сбора, объединения и хранения журналов приложений с функцией [объединения журналов][log-aggregation]. Функция объединения журналов делает доступ к журналам приложений более детерминированным, так как она объединяет журналы со всех контейнеров на рабочем узле и хранит их как один сводный файл журнала в файловой системе по умолчанию после завершения приложения. Ваше приложение может использовать сотни или тысячи контейнеров, но журналы для всех контейнеров, выполненных на одном рабочем узле, всегда будут объединены в один файл. В результате на каждый рабочий узел, используемый приложением, приходится один файл журнала. Объединение журналов включено по умолчанию в кластерах HDInsight (3.0 и более поздних версий), а сводные журналы можно найти в контейнере кластера по умолчанию по следующему адресу:

	wasb:///app-logs/<user>/logs/<applicationId>

где *user* — имя пользователя, запустившего приложение, а *applicationId* — уникальный идентификатор приложения, назначенный диспетчером ресурсов YARN.

Сводные журналы не могут считываться напрямую, так как они записаны в [TFile][T-file], [, двоичном формате][binary-format], индексированном контейнером. YARN предоставляет средства CLI для разгрузки этих журналов в виде обычного текста в нужные приложения или контейнеры. Вы можете просмотреть эти журналы в виде обычного текста, выполнив одну из следующих команд YARN непосредственно в узлах кластера (после подключения к нему через протокол RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

В следующем разделе рассказывается о том, как получить программный доступ к журналам приложений или контейнеров без необходимости использовать протокол RDP для подключения к кластерам HDInsight.

## <a name="enumerate-and-download"></a>Программное перечисление приложений и скачивание журналов

Чтобы использовать следующие примеры кода, необходимо скачать последнюю версию пакета SDK для HDInsight .NET, как указано выше. См. приведенные выше инструкции.

В приведенном ниже примере кода показано, как использовать новые интерфейсы API для перечисления приложений и загрузки журналов завершенных приложений.

> [AZURE.NOTE]Приведенные ниже интерфейсы API будут работать только с активными кластерами Hadoop 3.1.1.374 или более поздней версии. Добавьте следующие директивы:

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Это относится к новым интерфейсам API в приведенном ниже коде. Следующий фрагмент кода позволяет создать клиент журнала приложения в активном кластере вашей подписки:

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


Теперь с помощью клиента журнала приложения вы можете просматривать список завершенных приложений, фильтровать приложения на основе своих критериев и скачивать соответствующие журналы приложений. В следующем фрагменте кода показано, как это делается программным путем:

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

Приведенный выше код позволяет находить нужные приложения, используя клиент журнала приложения, а затем скачивать журналы этих приложений в локальную папку.

Кроме того, приведенный ниже фрагмент кода позволяет скачивать журналы приложений с известным уникальным идентификатором. Идентификатор приложения — это глобальный уникальный идентификатор приложения, назначаемый диспетчером ресурсов. Для его создания используется время запуска диспетчера ресурсов и монотонно возрастающий счетчик отправляемых в него приложений. Этот идентификатор выглядит следующим образом: application_&lt;RM-start-time&gt;_&lt;Counter&gt;. Идентификатор приложения и идентификатор задания нужно различать. Идентификатор задания — это понятие платформы MapReduce, а идентификатор приложения — понятие YARN, не зависящие от платформы. В YARN идентификатор задания — это идентификатор определенного задания MapReduce, обработанного мастером приложений MapReduce, отправленного в диспетчер ресурсов.

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

При необходимости можно скачать журналы для каждого контейнера (или для любого конкретного контейнера), используемого приложением, как показано ниже.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
 

<!---HONumber=July15_HO2-->
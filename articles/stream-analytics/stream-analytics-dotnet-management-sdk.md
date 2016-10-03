<properties
	pageTitle="SDK управления для .NET для Stream Analytics | Microsoft Azure"
	description="Приступая к работе с пакетом SDK для .NET для управления Stream Analytics. Дополнительная информация о настройке и выполнении заданий аналитики: создание проекта, входные и выходные данные и преобразования."
	keywords="SDK .net, API аналитики"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>


# Управление SDK для .NET: настройка и запуск заданий аналитики с помощью API Azure Stream Analytics для .NET.

Узнайте, как настраивать и запускать задания аналитики с помощью API Azure Stream Analytics для .NET, используя SDK управления для .NET. Настраивайте проект, создавайте источники входных и выходных данных и преобразования, а также запускайте и останавливайте задания. Для выполнения заданий аналитики можно осуществлять потоковую передачу данных из хранилища больших двоичных объектов или из концентратора событий.

См. [справочную документацию по управлению API Stream Analytics для .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics является полностью управляемой службой, обеспечивающей низкую задержку и высокий уровень доступности, масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Stream Analytics дает клиентам возможность настраивать задания потоковой передачи данных для анализа потоков данных и выполнять их в режиме, близком к режиму реального времени.


## Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

- Установите Visual Studio 2012 или 2013.
- Скачанный и установленный [пакет SDK для Azure .NET](https://azure.microsoft.com/downloads/).
- Создайте группу ресурсов Azure в своей подписке. Ниже приведен пример сценария Azure PowerShell. Дополнительную информацию об Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

			# If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
			#Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-	Задайте источник входных данных и назначение выходных данных. Дальнейшие указания по настройке примера входных данных см. в разделе [Добавление входных данных](stream-analytics-add-inputs.md), а по настройке примера выходных данных — в разделе [Добавление выходных данных](stream-analytics-add-outputs.md).


## Настройка проекта

Чтобы создать задание аналитики с использованием API Stream Analytics для .NET, сначала настройте проект.

1. Создайте консольное приложение Visual Studio C# .NET.
2. В консоли диспетчера пакетов выполните следующие команды, чтобы установить пакеты NuGet. Первый — пакет SDK для .NET для управления Azure Stream Analytics. Второй — клиент Azure Active Directory, который будет использоваться для проверки подлинности.

		Install-Package Microsoft.Azure.Management.StreamAnalytics
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Добавьте следующий раздел **appSettings** в файл App.config:

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
		  <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
		</appSettings>


	Замените значения **SubscriptionId** и **ActiveDirectoryTenantId** идентификаторами подписки Azure и клиента. Вы можете получить эти значения, запустив следующий командлет Azure PowerShell:

		Get-AzureAccount

5. Добавьте следующие операторы **using** в файл исходного кода (Program.cs) в проекте:

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Добавьте вспомогательный метод проверки подлинности:

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior: PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });

		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();

		    if (result != null)
		    {
		        return result.AccessToken;
		    }

		    throw new InvalidOperationException("Failed to acquire token");
		}  


## Создание клиента управления Stream Analytics

Объект **StreamAnalyticsManagementClient** позволяет управлять заданием и его компонентами, такими как входные и выходные данные, а также преобразованием.

Добавьте следующий код в начало метода **Main**:

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());

	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Значение переменной **resourceGroupName** должно быть таким же, как имя группы ресурсов, созданной или выбранной на предварительных шагах.

Инструкции по автоматизации этапа создания задач, связанного с представлением учетных данных, см. в статье [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md).

В дальнейших разделах этой статьи предполагается, что этот код находится в начале метода **Main**.

## Создание задания Stream Analytics

Следующий код создает задание Stream Analytics в группе ресурсов, которую вы определили. Вы добавите входные и выходные данные, а также преобразование в задание позже.

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};

	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Создание источника входных данных Stream Analytics

Следующий код создает источник входных данных Stream Analytics с типом источника входных данных BLOB-объекта и сериализацией CSV. Чтобы создать источник входных данных концентратора событий, используйте **EventHubStreamInputDataSource** вместо **BlobStreamInputDataSource**. Аналогично можно настроить тип сериализации источника входных данных.

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};

	InputCreateOrUpdateResponse inputCreateResponse =
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Источники входных данных из хранилища больших двоичных объектов или из концентратора событий привязываются к определенному заданию. Чтобы использовать один источник для разных заданий, потребуется снова вызвать метод и указать другое имя задания.


## Тестирование источника входных данных Stream Analytics

Метод **TestConnection** тестирует, может ли задание Stream Analytics подключиться к источнику входных данных, а также другие аспекты, относящиеся к этому типу источника входных данных. Например, в источнике входных данных BLOB-объекта, созданном на более раннем шаге, метод проверит, может ли пара имени и ключа учетной записи хранения использоваться для подключения к учетной записи хранения, а также убедится, что указанный контекст существует.

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse =
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Создание назначения выходных данных Stream Analytics

Создание назначения выходных данных аналогично созданию источника входных данных Stream Analytics. Как и источники входных данных, назначения выходных данных привязываются к определенному заданию. Чтобы использовать одно назначение выходных данных для разных заданий, потребуется повторно вызвать метод и указать другое имя задания.

Следующий код создает назначение выходных данных (базу данных SQL Azure). Вы можете настроить тип данных и тип сериализации назначения выходных данных.

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};

	OutputCreateOrUpdateResponse outputCreateResponse =
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Тестирование назначения выходных данных Stream Analytics

Назначение выходных данных Stream Analytics также содержит метод **TestConnection** для тестирования подключений.

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse =
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Создание преобразования Stream Analytics

Следующий код создает преобразование Stream Analytics с запросом select * from Input и задает выделение одной единицы потоковой передачи для задания Stream Analytics. Дополнительную информацию о настройке единиц потоковой передачи см. в статье [Масштабирование заданий Azure Stream Analytics](stream-analytics-scale-jobs.md).


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};

	var transformationCreateResp =
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Как и входные и выходные данные, преобразования также привязываются к определенному заданию Stream Analytics, в котором они были созданы.

## Запуск задания Stream Analytics
После создания задания Stream Analytics и его входных и выходных данных, а также преобразований, можно запустить задание, вызвав метод **Start**.

В следующем примере кода запускается задание Stream Analytics с пользовательским временем запуска выходных данных "12 декабря 2012 г., 12:12:12 UTC":

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};

	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Остановка задания Stream Analytics
Вы можете остановить выполняющееся задание Stream Analytics, вызвав метод **Stop**.

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Удаление задания Stream Analytics
Метод **Delete** удалит задание, а также базовые вложенные ресурсы, включая входные данные, выходные данные и преобразование задания.

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Получение поддержки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).


## Дальнейшие действия

Вы изучили основы использования пакета SDK для .NET для создания и выполнения заданий аналитики. См. также:

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Использование пакета SDK для .NET для управления Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn889315.aspx)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0921_2016-->
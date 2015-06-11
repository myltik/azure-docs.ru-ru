<properties 
	pageTitle="Создание, отслеживание фабрик данных Azure и управление ими с помощью Data Factory SDK" 
	description="Узнайте, как программным способом создания, мониторинга и управления фабрики данных Azure с помощью пакета SDK фабрики данных." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Создание, отслеживание фабрик данных Azure и управление ими с помощью Data Factory .NET SDK
## Обзор
Создание, отслеживание фабрик данных и управление ими программным способом с помощью Data Factory .NET SDK. Эта статья содержит пошаговое руководство по созданию образца консольного приложения .NET, которое будет создавать и отслеживать фабрику данных. В разделе [Справочник по библиотеке классов данных фабрики][adf-class-library-reference] подробные сведения о данных фабрики .NET SDK.



## Предварительные требования

- Visual Studio 2012 или 2013
- Загрузите и установите [Azure .NET SDK][azure-developer-center]
- Загрузите и установите пакеты NuGet для фабрик данных Azure. Инструкции приведены в этом пошаговом руководстве.

## Пошаговое руководство
1. С помощью Visual Studio 2012 или 2013 создайте консольное приложение C# .NET.
	<ol type="a">
	<li>Запустите <b>Visual Studio 2012</b> или <b>Visual Studio 2013</b>.</li>
	<li>Щелкните <b>файл</b>, пункты <b>Создать</b>, и нажмите кнопку <b>проекта</b>.</li> 
	<li>Разверните <b>шаблоны</b>, и выберите <b>Visual C#</b>. В этом пошаговом руководстве используется C#, но можно использовать любой язык .NET.</li> 
	<li>Выберите <b>консольного приложения</b> в списке типов проектов справа.</li>
	<li>Введите <b>DataFactoryAPITestApp</b> для <b>имя</b>.</li> 
	<li>Выберите <b>C:\ADFGetStarted</b> для <b>расположение</b>.</li>
	<li>Нажмите кнопку <b>ОК</b>, чтобы создать проект.</li>
</ol>
2. Щелкните <b>средства</b>, пункты <b>Диспетчер пакетов NuGet</b>, и нажмите кнопку <b>консоли диспетчера пакетов</b>.
3.	В <b>консоли диспетчера пакетов</b>, выполните следующие команды по одному.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Добавьте следующий **appSetttings** раздел **App.config** файл. Они используются при помощи вспомогательного метода: **GetAuthorizationHeader**. 

	Замените значения **SubscriptionId** и **ActiveDirectoryTenantId** на идентификаторы подписки Azure и клиента. Эти значения можно получить, выполнив **Get-AzureAccount** из Azure PowerShell (может потребоваться сначала войти в систему с помощью Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Добавьте следующий **с помощью** инструкции к файлу исходного кода (Program.cs) в проекте.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. Добавьте следующий код, который создает экземпляр **DataPipelineManagementClient** класса **Main** метод. Этот объект служит для создания фабрики данных, связанной службы, входных и выходных таблиц и конвейера. Кроме того, этот объект используется для отслеживания фрагментов таблицы во время выполнения.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Добавьте следующий код, создающий **фабрика данных** для **Main** метод.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. Добавьте следующий код, создающий **связанные службы** для **Main** метод.
	> [AZURE.NOTE]**Имя учетной записи****ключ учетной записи****ConnectionString** 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Добавьте следующий код, создающий **входных и выходных таблицах** для **Main** метод. 

	Обратите внимание, что **FolderPath** для входного большого двоичного объекта имеет значение **adftutorial /** где **adftutorial** имя контейнера в хранилище больших двоичных объектов. Если этот контейнер не существует в хранилище Azure blob, создать контейнер с таким именем: **adftutorial** и отправки текстового файла в контейнер.
	
	Обратите внимание, что FolderPath выходные данные большого двоичного объекта равен: **adftutorial/apifactoryoutput / {срез}** где **срез** динамически рассчитывается на основе значения из **SliceStart** (запуск даты и времени каждого среза).

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Добавьте следующий код, **создает и активирует конвейера** для **Main** метод. Этот конвейер имеет **CopyActivity** принимающий **BlobSource** как источник и **BlobSink** как приемника. 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Добавьте следующий вспомогательный метод, используемый **Main** метод **программы** класса. Этот метод выводит диалоговое окно позволяет задать **имя пользователя** и **пароль** используемой для входа на портал Azure.
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
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
 
13. Добавьте следующий код, чтобы **Main** метод для получения состояния срез данных выходной таблицы. Существует только срез, ожидаемый в этом образце.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. Добавьте следующий код для получения выполнения сведения для фрагмента срез данных, чтобы **Main** метод.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. Постройте консольное приложение. Щелкните **построения** меню и нажмите кнопку **Построить решение**.
16. Убедитесь, что как минимум один файл существует в контейнере adftutorial в хранилище больших двоичных объектов Azure. В противном случае создайте файл Emp.txt в блокноте со следующим содержимым и передайте его в контейнер adftutorial.

        John, Doe
		Jane, Doe
	 
17. Запуск образца, нажав кнопку **отладки** -> **Начать отладку** меню.
18. Использовать портал предварительной версии Azure убедитесь, что фабрика данных: **APITutorialFactory** создается с следующие артефакты: 
	- Связанные службы: **LinkedService_AzureStorage** 
	- Таблицы: **TableBlobSource** и **TableBlobDestination**.
	- Конвейер: **PipelineBlobSample** 
18. Убедитесь, что выходной файл создается в **apifactoryoutput** папки в **adftutorial** контейнера.


## См. также

Статья | Описание
------ | ---------------
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика имеет полный справочные материалы по библиотеке классов .NET, командлеты, скрипта JSON, функции, и т. д... 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/

<!---HONumber=GIT-SubDir--> 
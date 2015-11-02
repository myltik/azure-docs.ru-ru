<properties 
	pageTitle="Создание, отслеживание фабрик данных Azure и управление ими с помощью пакета SDK фабрики данных" 
	description="Узнайте, как программным способом создавать, отслеживать и контролировать фабрики данных Azure с помощью пакета SDK фабрики данных." 
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
	ms.date="10/06/2015" 
	ms.author="spelluru"/>

# Создание, отслеживание фабрик данных Azure и управление ими с помощью пакета .NET SDK фабрики данных
## Обзор
Создание, отслеживание фабрик данных и управление ими программным способом с помощью пакета .NET SDK фабрики данных. Эта статья содержит пошаговое руководство по созданию образца консольного приложения .NET, которое будет создавать и отслеживать фабрику данных. См. подробные сведения о пакете .NET SDK фабрики данных в [справочнике по библиотеке классов фабрики данных][adf-class-library-reference].



## Предварительные требования

- Visual Studio 2012 или 2013
- Скачайте и установите пакет [Azure .NET SDK][azure-developer-center].
- Загрузите и установите пакеты NuGet для фабрик данных Azure. Инструкции приведены в этом пошаговом руководстве.

## Пошаговое руководство
1. С помощью Visual Studio 2012 или 2013 создайте консольное приложение C# .NET.
	<ol type="a">
	<li>Запустите <b>Visual Studio 2012</b> или <b>Visual Studio 2013</b>.</li>
	<li>Щелкните <b>Файл</b>, наведите указатель мыши на пункт <b>Создать</b> и щелкните <b>Проект</b>.</li> 
	<li>Разверните раздел <b>Шаблоны</b> и выберите <b>Visual C#</b>. В этом пошаговом руководстве используется C#, но можно использовать любой язык .NET.</li> 
	<li>Выберите <b>Консольное приложение</b> в списке типов проектов справа.</li>
	<li>Введите <b>DataFactoryAPITestApp</b> в поле <b>Имя</b>.</li> 
	<li>В поле <b>Расположение</b> выберите <b>C:\ADFGetStarted</b>.</li>
	<li>Нажмите кнопку <b>ОК</b>, чтобы создать проект.</li>
</ol>
2. Щелкните <b>Инструменты</b>, наведите указатель мыши на <b>Диспетчер пакетов NuGet</b> и щелкните <b>Консоль диспетчера пакетов</b>.
3.	В окне <b>Консоль диспетчера пакетов</b> последовательно выполните следующие команды.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Добавьте следующий раздел **appSetttings** в файл **App.config**. Они используются вспомогательным методом **GetAuthorizationHeader**. 

	Замените значения **SubscriptionId** и **ActiveDirectoryTenantId** на идентификаторы подписки Azure и клиента. Эти значения можно получить, выполнив **Get-AzureAccount** из Azure PowerShell (может потребоваться сначала войти в систему с помощью Add-AzureAccount).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
6. Добавьте следующие операторы **using** в файл исходного кода (Program.cs) в проекте.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. Добавьте в метод **Main** следующий код, который создает экземпляр класса **DataPipelineManagementClient**. Этот объект служит для создания фабрики данных, связанной службы, входных и выходных наборов данных и конвейера. Кроме того, этот объект используется для отслеживания фрагментов набора данных во время выполнения.    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE]Замените **resourcegroupname** на имя своей группы ресурсов Azure. Для создания группы ресурсов можно использовать командлет [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx).

7. Добавьте следующий код, создающий **фабрику данных**, в метод **Main**.

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

8. Добавьте следующий код, создающий **связанную службу**, в метод **Main**.

	> [AZURE.NOTE]Укажите **имя учетной записи** и **ключ учетной записи** для своей учетной записи хранения Azure в **ConnectionString**.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. Добавьте следующий код, создающий **входные и выходные наборы данных**, в метод **Main**. 

	Обратите внимание, что **FolderPath** для входного большого двоичного объекта задан как **adftutorial/**, где **adftutorial** — это имя контейнера в хранилище больших двоичных объектов. Если этот контейнер не существует в хранилище больших двоичных объектов Azure, создайте контейнер с именем **adftutorial** и отправьте в него текстовый файл.
	
	Обратите внимание, что FolderPath для выходного большого двоичного объекта задан как **adftutorial/apifactoryoutput/{срез}**, где **срез** динамически рассчитывается на основе значения **SliceStart** (начальные дата и время каждого среза).

 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
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

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
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
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. Добавьте следующий код, который **создает и активирует конвейер**, в метод **Main**. В этом конвейер есть действие **CopyActivity**, принимающие **BlobSource** как источник и **BlobSink** как приемник.

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

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. Добавьте следующий вспомогательный метод, используемый методом **Main**, в класс **Program**. Этот метод выводит диалоговое окно, которое позволяет ввести **имя пользователя** и **пароль**, используемые для входа на портал Azure.
 
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
 
13. Добавьте следующий код в метод **Main**, чтобы получить состояние среза данных выходного набора данных. Существует только срез, ожидаемый в этом образце.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **(Необязательно)** Добавьте в метод **Main** следующий код для получения сведений о выполнении для среза данных.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
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

15. В обозревателе решений разверните проект (**DataFactoryAPITestApp**), щелкните правой кнопкой мыши **Ссылки** и затем щелкните **Добавить ссылку**. Установите флажок для сборки **System.Configuration** и нажмите кнопку **ОК**.
16. Постройте консольное приложение. В меню щелкните **Построить** и выберите **Построить решение**. 
16. Убедитесь, что как минимум один файл существует в контейнере adftutorial в хранилище больших двоичных объектов Azure. В противном случае создайте файл Emp.txt в блокноте со следующим содержимым и передайте его в контейнер adftutorial.

        John, Doe
		Jane, Doe
	 
17. Запустите пример, щелкнув **Отладка** > **Начать отладку** в меню. При появлении сообщения **Получение сведений о выполнении для среза данных** подождите несколько минут и нажмите клавишу **ВВОД**.
18. Используйте портал предварительной версии Azure, чтобы убедиться, что фабрика данных **APITutorialFactory** создана с использованием следующих артефактов: 
	- Связанная служба: **LinkedService\_AzureStorage**. 
	- Набор данных: **DatasetBlobSource** и **DatasetBlobDestination**.
	- Конвейер: **PipelineBlobSample**. 
18. Убедитесь, что выходной файл создан в папке **apifactoryoutput** в контейнере **adftutorial**.



> [AZURE.NOTE]Приведенный выше образец кода запускает диалоговое окно для ввода учетных данных Azure. Если требуется выполнить вход программным образом без применения диалогового окна, см. раздел [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell).


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=Oct15_HO4-->
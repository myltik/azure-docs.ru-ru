<properties 
	pageTitle="Руководство. Создание конвейера с действием копирования с помощью API .NET | Microsoft Azure" 
	description="В этом руководстве описано, как с помощью API .NET создать конвейер фабрики данных Azure с действием копирования." 
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
	ms.topic="get-started-article" 
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# Руководство. Создание конвейера с действием копирования с помощью API .NET
> [AZURE.SELECTOR]
- [Обзор и предварительные требования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)

В этом руководстве описывается создание и мониторинг фабрики данных Azure с помощью API .NET. Конвейер в фабрике данных копирует данные из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью действия копирования.

Действие копирования перемещает данные в фабрике данных Azure. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).

> [AZURE.NOTE] 
В этой статье рассматриваются не все API-интерфейсы .NET фабрики данных. Подробные сведения о пакете SDK для .NET в фабрике данных см. в [справочнике по API-интерфейсам .NET фабрики данных](https://msdn.microsoft.com/library/mt415893.aspx).

## Предварительные требования
- Ознакомьтесь с [обзором руководства](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) и выполните **предварительные требования**.
- Visual Studio 2012, 2013 или 2015
- Скачайте и установите пакет [Azure .NET SDK](http://azure.microsoft.com/downloads/).
- Azure PowerShell. Далее, чтобы установить Azure PowerShell на локальном компьютере, следуйте указаниям в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). С помощью Azure PowerShell вы создадите приложение Azure Active Directory.

### Создание приложения в Azure Active Directory
Вы создадите приложение Azure Active Directory и субъект-службу для приложения, а затем назначите роль **Участник Data Factory**.

1. Запустите **PowerShell**.
1. Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	
		Login-AzureRmAccount   
2. Выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи.

		Get-AzureRmSubscription 
3. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените **&lt;NameOfAzureSubscription&gt;** именем подписки Azure.

		Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

	> [AZURE.IMPORTANT] Запишите значения **SubscriptionId** и **TenantId**, указанные в выходных данных этой команды.
4. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду в PowerShell.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Если группа ресурсов уже есть, укажите, требуется или не требуется ее обновить (Y или N соответственно).

	Если вы используете другую группу ресурсов, укажите ее имя вместо ADFTutorialResourceGroup.
5. Создайте приложение Azure Active Directory.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

	Если возникнет следующая ошибка, укажите другой URL-адрес и запустите команду еще раз.

		Another object with the same value for property identifierUris already exists.

6. Создайте субъект-службу AD.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Назначьте субъекту-службе роль **Участник Data Factory**.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Получите идентификатор приложения.

		$azureAdApplication

	Запишите идентификатор приложения (**applicationID** в выходных данных).

Вы должны получить следующие четыре значения:

- Tenant ID
- Идентификатор подписки
- Идентификатор приложения
- пароль (указан в первой команде).

## Пошаговое руководство
1. С помощью Visual Studio 2012, 2013 или 2015 создайте консольное приложение C# .NET.
	1. Запустите **Visual Studio** 2012, 2013 или 2015.
	2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
	3. Разверните раздел **Шаблоны** и выберите **Visual C#**. В этом пошаговом руководстве используется C#, но можно использовать любой язык .NET.
	4. Выберите **Консольное приложение** в списке типов проектов справа.
	5. В качестве имени введите **DataFactoryAPITestApp**.
	6. В качестве расположения укажите **C:\\ADFGetStarted**.
	7. Нажмите кнопку **ОК**, чтобы создать проект.
2. Щелкните **Инструменты**, наведите указатель мыши на элемент **Диспетчер пакетов NuGet** и щелкните **Консоль диспетчера пакетов**.
3.	В окне **Консоль диспетчера пакетов** последовательно выполните следующие команды.

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
6. Добавьте следующий раздел **appSetttings** в файл **App.config**. Эти параметры используются вспомогательным методом **GetAuthorizationHeader**.

	Замените значения **&lt;идентификатора приложения&gt;**, **&lt;пароля&gt;**, **&lt;идентификатора подписки&gt;** и **&lt;идентификатора клиента&gt;** собственными значениями.

		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
    		<add key="ApplicationId" value="<Application ID>" />
    		<add key="Password" value="<Password>" />    
		    <add key="SubscriptionId" value= "Subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="tenant ID" />
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
6. Добавьте в метод **Main** следующий код, который создает экземпляр класса **DataPipelineManagementClient**. Этот объект используется не только для создания фабрики данных, связанной службы, входных и выходных наборов данных, а также конвейера, но и для отслеживания срезов набора данных при выполнении.

			// create data factory management client
        	string resourceGroupName = "ADFTutorialResourceGroup";
        	string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.IMPORTANT] 
	Замените значение **resourceGroupName** именем своей группы ресурсов Azure.
	> 
	> Измените значение имени фабрики данных (**dataFactoryName**), чтобы оно было глобально уникальным. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.

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

8. Добавьте следующий код, который создает **связанную службу хранилища Azure**, в метод **Main**.

	> [AZURE.IMPORTANT] Замените значения **storageaccountname** и **accountkey** именем и ключом вашей учетной записи хранения Azure.

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Добавьте следующий код, который создает **связанную службу Azure SQL**, в метод **Main**.
 
	> [AZURE.IMPORTANT] Укажите вместо **servername**, **databasename**, **username** и **password** имя своего сервера Azure SQL Server, имя базы данных, имя пользователя и пароль.

			// create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Добавьте следующий код, создающий **входные и выходные наборы данных**, в метод **Main**.

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
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


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
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
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

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
                                    Name = "BlobToAzureSql",
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

12. Добавьте следующий код в метод **Main**, чтобы получить состояние среза данных выходного набора данных. Существует только срез, ожидаемый в этом образце.
 
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

14. Добавьте в метод **Main** следующий код, чтобы получить сведения о выполнении для среза данных.

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

13. Добавьте следующий вспомогательный метод, используемый методом **Main**, в класс **Program**.
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. В обозревателе решений разверните проект (**DataFactoryAPITestApp**), щелкните правой кнопкой мыши **Ссылки** и затем щелкните **Добавить ссылку**. Установите флажок для сборки **System.Configuration** и щелкните **ОК**.
16. Постройте консольное приложение. В меню щелкните **Построить** и выберите **Построить решение**.
16. Убедитесь, что в контейнере **adftutorial** в хранилище BLOB-объектов Azure есть как минимум один файл. В противном случае создайте в блокноте файл **Emp.txt** со следующим содержимым, а затем отправьте его в контейнер adftutorial.

        John, Doe
		Jane, Doe
	 
17. Запустите пример, щелкнув **Отладка** > **Начать отладку** в меню. При появлении сообщения **Получение сведений о выполнении для среза данных** подождите несколько минут и нажмите клавишу **ВВОД**.
18. Перейдите на портал Azure и убедитесь, что фабрика данных **APITutorialFactory** создана с использованием следующих артефактов:
	- Связанная служба: **LinkedService\_AzureStorage**.
	- Набор данных: **DatasetBlobSource** и **DatasetBlobDestination**.
	- Конвейер: **PipelineBlobSample**.
18. Убедитесь, что выходной файл создан в папке **apifactoryoutput** в контейнере **adftutorial**.

## Дальнейшие действия

- В статье [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md) подробно описывается действие копирования, используемое в этом руководстве.
- Подробные сведения о пакете SDK для .NET в фабрике данных см. в [справочнике по API-интерфейсам .NET фабрики данных](https://msdn.microsoft.com/library/mt415893.aspx). В этой статье рассматриваются не все API-интерфейсы .NET фабрики данных.

 

<!---HONumber=AcomDC_0921_2016-->
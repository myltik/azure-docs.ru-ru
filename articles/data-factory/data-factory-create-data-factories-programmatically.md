---
title: Создание, отслеживание фабрик данных Azure и управление ими с помощью пакета SDK фабрики данных | Microsoft Docs
description: Узнайте, как программным способом создавать, отслеживать и контролировать фабрики данных Azure с помощью пакета SDK фабрики данных.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: spelluru

---
# Создание, отслеживание фабрик данных Azure и управление ими с помощью пакета .NET SDK фабрики данных
## Обзор
Создание, отслеживание фабрик данных и управление ими программным способом с помощью пакета .NET SDK фабрики данных. Эта статья содержит пошаговое руководство по созданию образца консольного приложения .NET, которое будет создавать и отслеживать фабрику данных. См. подробные сведения о Data Factory .NET SDK в [справочнике по библиотеке классов фабрики данных](https://msdn.microsoft.com/library/mt415893.aspx).

## Предварительные требования
* Visual Studio 2012, 2013 или 2015
* Скачанный и установленный [пакет SDK для Azure .NET](http://azure.microsoft.com/downloads/).
* Добавьте собственное клиентское приложение в Azure Active Directory. Инструкции по добавлению приложения см. в статье [Интеграция приложений с Azure Active Directory](../active-directory/active-directory-integrating-applications.md). Запишите значения **Идентификатор клиента** и **URI перенаправления**, отображенные на странице **Настройка**.
* Получите значения **Идентификатор подписки** и **Идентификатор клиента**. Инструкции см. в разделе [Получение идентификаторов подписки и клиента Azure](#get-azure-subscription-and-tenant-ids).
* Загрузите и установите пакеты NuGet для фабрик данных Azure. Инструкции приведены в этом пошаговом руководстве.

## Пошаговое руководство
1. С помощью Visual Studio 2012 или 2013 создайте консольное приложение C# .NET.
   1. Запустите **Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015**.
   2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
   3. Разверните раздел **Шаблоны** и выберите **Visual C#**. В этом пошаговом руководстве используется C#, но можно использовать любой язык .NET.
   4. Выберите **Консольное приложение** в списке типов проектов справа.
   5. Введите **DataFactoryAPITestApp** в поле **Имя**.
   6. В поле **Расположение** выберите **C:\\ADFGetStarted**.
   7. Нажмите кнопку **ОК**, чтобы создать проект.
2. Щелкните **Инструменты**, наведите указатель мыши на **Диспетчер пакетов NuGet** и щелкните **Консоль диспетчера пакетов**.
3. В окне **Консоль диспетчера пакетов** последовательно выполните следующие команды.
   
         Install-Package Microsoft.Azure.Management.DataFactories
         Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
4. Добавьте следующий раздел **appSetttings** в файл **App.config**. Эти значения конфигурации используются в методе **GetAuthorizationHeader**.
   
   > [!IMPORTANT]
   > Замените значения **AdfClientId**, **RedirectUri**, **SubscriptionId** и **ActiveDirectoryTenantId** собственными значениями.
   > 
   > 
   
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
   
            <!-- Replace the following values with your own -->
            <add key="AdfClientId" value="Your AAD application ID" />
            <add key="RedirectUri" value="Your AAD application's redirect URI" />
            <add key="SubscriptionId" value="your subscription ID" />
            <add key="ActiveDirectoryTenantId" value="your tenant ID" />
        </appSettings>
5. Добавьте следующие операторы **using** в файл исходного кода (Program.cs) в проекте.
   
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
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";
   
        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());
   
        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
   
        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
   
   > [!NOTE]
   > Замените **resourcegroupname** на имя своей группы ресурсов Azure. Для создания группы ресурсов можно использовать командлет [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx).
   > 
   > 
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
   
   > [!NOTE]
   > Укажите **имя учетной записи** и **ключ учетной записи** для своей учетной записи хранения Azure в **ConnectionString**.
   > 
   > 
   
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
   
    **FolderPath** для входного большого двоичного объекта задан как **adftutorial/**, где **adftutorial** — это имя контейнера в хранилище BLOB-объектов. Если этот контейнер не существует в хранилище больших двоичных объектов Azure, создайте контейнер с именем **adftutorial** и отправьте в него текстовый файл.
   
    FolderPath для выходного большого двоичного объекта задан как **adftutorial/apifactoryoutput/{срез}**, где **срез** динамически рассчитывается на основе значения **SliceStart** (начальные дата и время каждого среза).
   
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
10. Добавьте следующий код, который **создает и активирует конвейер**, в метод **Main**. В этом конвейер есть действие **CopyActivity**, принимающие **BlobSource** как источник и **BlobSink** как приемник.
    
    Действие копирования перемещает данные в фабрике данных Azure. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).
    
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
11. Добавьте следующий вспомогательный метод, используемый методом **Main**, в класс **Program**. Этот метод выводит диалоговое окно, которое позволяет ввести **имя пользователя** и **пароль**, используемые для входа на портал Azure.
    
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
13. **(Необязательно.)** Добавьте в метод **Main** следующий код для получения сведений о выполнении для среза данных.
    
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
14. В обозревателе решений разверните проект (**DataFactoryAPITestApp**), щелкните правой кнопкой мыши **Ссылки** и затем щелкните **Добавить ссылку**. Установите флажок для сборки `System.Configuration` и нажмите кнопку **ОК**.
15. Постройте консольное приложение. В меню щелкните **Построить** и выберите **Построить решение**.
16. Убедитесь, что как минимум один файл существует в контейнере adftutorial в хранилище больших двоичных объектов Azure. В противном случае создайте файл Emp.txt в блокноте со следующим содержимым и передайте его в контейнер adftutorial.
    
        John, Doe
        Jane, Doe 
17. Запустите пример, щелкнув **Отладка** > **Начать отладку** в меню. При появлении сообщения **Получение сведений о выполнении для среза данных** подождите несколько минут и нажмите клавишу **ВВОД**.
18. Перейдите на портал Azure и убедитесь, что фабрика данных **APITutorialFactory** создана с использованием следующих артефактов:
    * Связанная служба: **LinkedService\_AzureStorage**.
    * Набор данных: **DatasetBlobSource** и **DatasetBlobDestination**.
    * Конвейер: **PipelineBlobSample**.
19. Убедитесь, что выходной файл создан в папке **apifactoryoutput** в контейнере **adftutorial**.

## Вход без всплывающего диалогового окна
Приведенный выше в пошаговом руководстве пример кода запускает диалоговое окно для ввода учетных данных Azure. Если требуется выполнить программный вход без применения диалогового окна, ознакомьтесь с разделом [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell).

> [!IMPORTANT]
> Добавьте веб-приложение в Azure Active Directory и запишите идентификатор клиента и секрет клиента приложения.
> 
> 

### Пример
Создайте метод GetAuthorizationHeaderNoPopup.

    public static string GetAuthorizationHeaderNoPopup()
    {
        var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        var context = new AuthenticationContext(authority.AbsoluteUri);
        var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
        AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }

Замените вызов **GetAuthorizationHeader** вызовом **GetAuthorizationHeaderNoPopup** в функции **Main**.

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeaderNoPopup());

Ниже объясняется, как создать приложение Active Directory и субъект-службу и назначить ее роли "Участник фабрики данных".

1. Создайте приложение AD.
   
        $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
2. Создайте субъект-службу AD.
   
        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
3. Добавьте субъект-службу в роль "Участник фабрики данных".
   
        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
4. Получите идентификатор приложения.
   
        $azureAdApplication

Запишите идентификатор приложения и пароль (секрет клиента) и используйте их в пошаговом руководстве.

## Получение идентификаторов подписки и клиента Azure
Если на вашем компьютере не установлена последняя версия Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md), чтобы установить ее.

1. Запустите Azure PowerShell и выполните следующую команду.
2. Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
   
        Login-AzureRmAccount
   
    Если с этой учетной записью связана только одна подписка Azure, то следующие два шага выполнять н нужно.
3. Выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи.
   
        Get-AzureRmSubscription
4. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените **NameOfAzureSubscription** именем своей подписки Azure.
   
        Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext 

Запишите значения **SubscriptionId** и **TenantId**.

<!---HONumber=AcomDC_0914_2016-->
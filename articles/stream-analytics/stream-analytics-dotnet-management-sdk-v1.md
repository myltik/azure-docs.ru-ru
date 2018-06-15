---
title: Пакет SDK для .NET версии 1.х для управления Azure Stream Analytics
description: Приступая к работе с пакетом SDK для .NET для управления Stream Analytics. Узнайте, как настраивать и выполнять задания аналитики. Создание проектов, входные и выходные данные, преобразования.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/06/2017
ms.openlocfilehash: a8f272403a0c6903521acc7cc45935c4e5bc419c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528109"
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Управление SDK для .NET версии 1.x: настройка и запуск заданий аналитики с помощью API Azure Stream Analytics для .NET
Узнайте, как настраивать и запускать задания аналитики с помощью API Azure Stream Analytics для .NET, используя управление SDK для .NET. Настраивайте проект, создавайте источники входных и выходных данных и преобразования, а также запускайте и останавливайте задания. Для выполнения заданий аналитики можно осуществлять потоковую передачу данных из хранилища больших двоичных объектов или из концентратора событий.

См. [справочную документацию по управлению API Stream Analytics для .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics является полностью управляемой службой, обеспечивающей низкую задержку и высокий уровень доступности, масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Stream Analytics дает клиентам возможность настраивать задания потоковой передачи данных для анализа потоков данных и выполнять их в режиме, близком к режиму реального времени.  

> [!NOTE]
> Пример кода в этой статье по-прежнему использует устаревшую версию (1.х) управления SDK для .NET. Пример кода с использованием последней версии пакета SDK см. в статье [Управление SDK для .NET: настройка и запуск заданий аналитики с помощью API Azure Stream Analytics для .NET](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этой статьей необходимо иметь следующее:

* Установленный экземпляр Visual Studio 2017 или Visual Studio 2015.
* Скачанный и установленный [пакет SDK для Azure .NET](https://azure.microsoft.com/downloads/).
* Создайте группу ресурсов Azure в своей подписке. Ниже приведен пример сценария Azure PowerShell. Дополнительную информацию об Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](/powershell/azure/overview).  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Задайте источник входных данных и назначение выходных данных для задания подключения.

## <a name="set-up-a-project"></a>Настройка проекта
Чтобы создать задание аналитики с использованием API Stream Analytics для .NET, сначала настройте проект.

1. Создайте консольное приложение Visual Studio C# .NET.
2. В консоли диспетчера пакетов выполните следующие команды, чтобы установить пакеты NuGet. Первый — пакет SDK для .NET для управления Azure Stream Analytics. Второй — клиент Azure Active Directory, который будет использоваться для проверки подлинности.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Добавьте следующий раздел **appSettings** в файл App.config:
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Замените значения **SubscriptionId** и **ActiveDirectoryTenantId** идентификаторами подписки Azure и клиента. Вы можете получить эти значения, запустив следующий командлет Azure PowerShell:

        Get-AzureAccount

4. Добавьте следующую ссылку в CSPROJ-файле:

        <Reference Include="System.Configuration" />

1. Добавьте следующие операторы **using** в файл исходного кода (Program.cs) в проекте:
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Добавьте вспомогательный метод проверки подлинности:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Создание клиента управления Stream Analytics
Объект **StreamAnalyticsManagementClient** позволяет управлять заданием и его компонентами, такими как входные и выходные данные, а также преобразованием.

Добавьте следующий код в начало метода **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Значение переменной **resourceGroupName** должно быть таким же, как имя группы ресурсов, созданной или выбранной на предварительных шагах.

Инструкции по автоматизации этапа создания задач, связанного с представлением учетных данных, см. в статье [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../azure-resource-manager/resource-group-authenticate-service-principal.md).

В дальнейших разделах этой статьи предполагается, что этот код находится в начале метода **Main** .

## <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics
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


## <a name="create-a-stream-analytics-input-source"></a>Создание источника входных данных Stream Analytics
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

## <a name="test-a-stream-analytics-input-source"></a>Тестирование источника входных данных Stream Analytics
Метод **TestConnection** тестирует, может ли задание Stream Analytics подключиться к источнику входных данных, а также другие аспекты, относящиеся к этому типу источника входных данных. Например, в источнике входных данных BLOB-объекта, созданном на более раннем шаге, метод проверит, может ли пара имени и ключа учетной записи хранения использоваться для подключения к учетной записи хранения, а также убедится, что указанный контекст существует.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Создание назначения выходных данных Stream Analytics
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

## <a name="test-a-stream-analytics-output-target"></a>Тестирование назначения выходных данных Stream Analytics
Назначение выходных данных Stream Analytics также содержит метод **TestConnection** для тестирования подключений.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Создание преобразования Stream Analytics
Следующий код создает преобразование Stream Analytics с запросом select * from Input и задает выделение одной единицы потоковой передачи для задания Stream Analytics. Дополнительные сведения о настройке единиц потоковой передачи см. в статье [Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных](stream-analytics-scale-jobs.md).

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

## <a name="start-a-stream-analytics-job"></a>Запуск задания Stream Analytics
После создания задания Stream Analytics и его входных и выходных данных, а также преобразований, можно запустить задание, вызвав метод **Start** .

В следующем примере кода запускается задание Stream Analytics с пользовательским временем запуска выходных данных "12 декабря 2012 г., 12:12:12 UTC":

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Остановка задания Stream Analytics
Вы можете остановить выполняющееся задание Stream Analytics, вызвав метод **Stop** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Удаление задания Stream Analytics
Метод **Delete** удалит задание, а также базовые вложенные ресурсы, включая входные данные, выходные данные и преобразование задания.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Получение поддержки
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дополнительная информация
Вы изучили основы использования пакета SDK для .NET для создания и выполнения заданий аналитики. См. также:

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Использование пакета SDK для .NET для управления Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn889315.aspx)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

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
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

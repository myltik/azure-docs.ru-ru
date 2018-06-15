---
title: Создание фабрики данных Azure с помощью REST API | Документация Майкрософт
description: Создайте фабрику данных Azure для копирования данных из одного расположения в хранилище BLOB-объектов Azure в другое.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 7c5f36fec870a81e023c4cde74c9e878f2f544f3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593344"
---
# <a name="create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Создание фабрики данных Azure и конвейера с помощью REST API
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-rest-api.md)

Фабрика данных Azure — это облачная служба интеграции данных, которая позволяет создавать управляемые данными рабочие процессы в облаке для оркестрации и автоматизации перемещения и преобразования данных. С помощью фабрики данных Azure можно создавать и включать в расписание управляемые данными рабочие процессы (конвейеры), которые могут принимать данные из разнородных хранилищ данных, обрабатывать и преобразовывать эти данные с помощью служб вычислений (например, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics и машинного обучения Azure), а также публиковать выходные данные в хранилища данных (например, хранилище данных SQL Azure) для использования приложениями бизнес-аналитики. 

В этом кратком руководстве описано создание фабрики данных Azure с помощью REST API. В этой фабрике данных конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. Если у вас нет подписки, вы можете [создать бесплатную пробную учетную запись](http://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранения Azure.** Хранилище BLOB-объектов используется как хранилище данных **источник** и **приемник**. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Создайте **контейнер больших двоичных объектов** в хранилище BLOB-объектов, в контейнере создайте входную **папку** и отправьте несколько файлов в нее. Такие средства, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), можно использовать для подключения к хранилищу BLOB-объектов Azure, создания контейнера BLOB-объектов, отправки входного файла и проверки выходного.
* Установите **Azure PowerShell**. Следуйте инструкциям по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps). В этом руководстве используется PowerShell для вызова REST API.
* [Используйте следующие инструкции](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), **чтобы создать приложение в Azure Active Directory**. Запишите следующие значения, которые вы используете в следующих шагах: **идентификатор приложения**, **ключ аутентификации** и **идентификатор клиента**. Назначьте приложению роль **Участник**.

## <a name="set-global-variables"></a>Настройка глобальных переменных

1. Запустите **PowerShell**. Не закрывайте Azure PowerShell, пока выполняются описанные в этом кратком руководстве инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.

    Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
        
    ```powershell
    Connect-AzureRmAccount
    ```        
    Чтобы просмотреть все подписки для этой учетной записи, выполните следующую команду:

    ```powershell
    Get-AzureRmSubscription
    ```
    Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените значение **SubscriptionId** на идентификатор подписки Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Выполните следующие команды после замены заполнителей собственными значениями, чтобы задать глобальные переменные, используемые в последующих шагах.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2017-09-01-preview"
    ```

## <a name="authenticate-with-azure-ad"></a>Проверка подлинности с помощью Azure AD

Выполните следующую команды для проверки подлинности с помощью Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
} 
```

## <a name="create-a-data-factory"></a>Создание фабрики данных

Чтобы создать фабрику данных, выполните следующие команды:

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

Ниже приведен пример ответа:

```json

{
    "name":  "<dataFactoryName>",
    "tags": {

            },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2017-09-01-preview"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
} 

```

## <a name="create-linked-services"></a>Создание связанных служб

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве необходимо создать одну связанную службу хранилища Azure для копирования хранилища-источника и приемника, который в примере называется AzureStorageLinkedService.

Выполните следующие команды для создания связанной службы с именем **AzureStorageLinkedService**:

Перед выполнением команд замените значения &lt;accountname&gt; и &lt;accountkey&gt; на имя вашей учетной записи хранения Azure и ее ключ.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                "type": "SecureString"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Пример выходных данных:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
                       "type":  "AzureStorage",
                       "typeProperties":  {
                                              "connectionString":  "@{value=**********; type=SecureString}"
                                          }
                   },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>Создание наборов данных

Вы можете определить набор данных, который будет представлять данные для копирования из источника в приемник. В этом примере набор данных большого двоичного объекта относится к связанным службам хранилища Azure, созданным на предыдущем шаге. Набор данных принимает параметр, значение которого задается в действии, использующем набор данных. Параметр используется для создания folderPath, указывающего, где находятся или хранятся данные.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Пример выходных данных:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
                       "type":  "AzureBlob",
                       "typeProperties":  {
                                              "folderPath":  "@{value=@{dataset().path}; type=Expression}"
                                          },
                       "linkedServiceName":  {
                                                 "referenceName":  "AzureStorageLinkedService",
                                                 "type":  "LinkedServiceReference"
                                             },
                       "parameters":  {
                                          "path":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>Создание конвейера

В этом примере этот конвейер содержит одно действие и принимает два параметра: путь входного и выходного большого двоичного объекта. Значения для этих параметров устанавливаются при активации или выполнении конвейера. Действие копирования ссылается на тот же набор данных большого двоичного объекта, который был создан на предыдущем шаге, в качестве входного и выходного. Если набор данных используется в качестве входного, указывается путь к входным данным. И если набор данных используется в качестве выходного, указывается путь к выходным данным. 

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Пример выходных данных:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
                       "activities":  [
                                          "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
                                      ],
                       "parameters":  {
                                          "inputPath":  "@{type=String}",
                                          "outputPath":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>Создание конвейера

В этом шаге параметрам **inputPath** и **outputPath** задаются значения, указываемые в конвейере с фактическими значениями путей большого двоичного объекта источника и приемника, а также активируется конвейер. Идентификатор контейнера, возвращаемый в теле ответа, используется позже при мониторинге API.

Замените значения **inputPath** и **outputPath** своими путями к источнику и приемнику больших двоичных объектов для копирования данных до сохранения файла.


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Пример выходных данных:

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>Отслеживание конвейера

1. Запустите следующий скрипт, чтобы проверять состояние выполнения, пока не закончится копирование данных.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Пример выходных данных:

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
                        "inputPath: <inputBlobPath>",
                        "outputPath: <outputBlobPath>"
                    ],
        "parametersCount":  2,
        "parameterNames":  [
                            "inputPath",
                            "outputPath"
                        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
                                "<inputBlobPath>",
                                "<outputBlobPath>"
                            ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. Запустите следующий скрипт, извлекающий сведения о выполнении действия копирования, например размер записанных и прочитанных данных.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    Пример выходных данных:

    ```json
    {
        "value":  [
                    {
                        "id":  "000000000-0000-0000-0000-00000000000",
                        "timestamp":  "2017-09-07T13:12:38.4780542Z",
                        "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                        "pipelineName":  "Adfv2QuickStartPipeline",
                        "status":  "Succeeded",
                        "failureType":  "",
                        "linkedServiceName":  "",
                        "activityName":  "CopyFromBlobToBlob",
                        "activityType":  "Copy",
                        "activityStart":  "2017-09-07T13:12:02.3299261Z",
                        "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                        "duration":  36148,
                        "input":  "@{source=; sink=}",
                        "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedCloudDataMovementUnits=2; billedDuration=22}",
                        "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
                    }
                ]
    }
    ```

## <a name="verify-the-output"></a>Проверка выходных данных

Воспользуйтесь обозревателем службы хранилища Azure, чтобы проверить, скопирован ли большой двоичный объект в outputBlobPath из inputBlobPath, как указано при создании конвейера.

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ресурсы, созданные в ходе работы с этим руководством, двумя способами. Вы можете удалить [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md), которая содержит все связанные ресурсы. Если же вы хотите сохранить другие ресурсы, удалите только фабрику данных, созданную в этом руководстве.

Выполните следующую команду, чтобы удалить всю группу ресурсов: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Выполните следующую команду для удаления только фабрики данных: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях. 

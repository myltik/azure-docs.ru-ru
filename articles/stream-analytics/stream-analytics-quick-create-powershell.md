---
title: Создание задания Stream Analytics с помощью Azure PowerShell
description: В этом кратком руководстве описывается, как с помощью модуля Azure PowerShell развернуть и запустить задание Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Краткое руководство по созданию задания Stream Analytics с помощью Azure PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командлетов или скриптов PowerShell. В этом кратком руководстве описывается, как с помощью модуля Azure PowerShell развернуть и запустить задание Azure Stream Analytics.

## <a name="before-you-begin"></a>Перед началом работы

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/).  

* Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Запустите `Get-Module -ListAvailable AzureRM`, чтобы найти версию, установленную на вашем локальном компьютере. Если вам необходимо выполнить установку или обновление, см. сведения в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Сценарий в этой статье описывает чтение данных из хранилища BLOB-объектов, их преобразование и запись обратно в другой контейнер в аналогичном хранилище.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в свою подписку Azure с помощью команды `Connect-AzureRmAccount` и введите учетные данные Azure во всплывающем окне браузера. После входа в систему, если у вас есть несколько подписок, выберите ту, которую вы хотите использовать при работе с этим кратким руководством, запустив следующие командлеты. Не забудьте заменить <your subscription> именем своей подписки:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Подготовка входных данных

Прежде чем определить задание Stream Analytics, необходимо подготовить данные, которые настроены как входные данные задания. Выполните следующие шаги для подготовки входных данных, необходимых для задания: 

1. Загрузите [пример данных датчика](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) с сайта GitHub.  

2. Создайте стандартную учетную запись хранения общего назначения с репликацией LRS с помощью командлета [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  

3. Получите контекст учетной записи хранения, определяющий необходимую учетную запись хранения. Работая в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные. В этом примере создается учетная запись хранения mystorageaccount с включенными по умолчанию локально избыточным хранилищем (LRS) и шифрованием больших двоичных объектов.  

4. Затем создайте контейнер с помощью командлета [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), установите для разрешений значение "blob", чтобы разрешить общий доступ к файлам, и передайте ранее загруженный [пример данных датчика](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json). 

Это можно сделать, выполнив следующий скрипт PowerShell:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

Создайте задание Stream Analytics с помощью командлета [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя группы ресурсов и определение задания в качестве параметров. Именем задания может быть любое понятное имя, которое идентифицирует ваше задание. Имя задания Stream Analytics может содержать только буквенно-цифровые символы, дефисы и подчеркивания. Длина должна составлять от 3 до 63 символов. Определение задания — это JSON-файл, содержащий свойства, необходимые для создания задания. На вашем локальном компьютере создайте файл с именем JobDefinition.json и добавьте в него следующие данные JSON:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Затем запустите командлет New-AzureRMStreamAnalyticsJob и обязательно замените значение переменной jobDefinitionFile на путь, по которому вы сохранили JSON-файл определения задания. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Настройка входных данных для задания

Добавьте входные данные в задание с помощью командлета [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя входных данных задания, имя группы ресурсов и определение входных данных задания в качестве параметров. Определение входных данных задания — это JSON-файл, который содержит свойства, необходимые для настройки входных данных задания. В этом примере вы создадите хранилище BLOB-объектов в качестве входных данных. На локальном компьютере создайте файл с именем JobInputDefinition.json и добавьте к нему следующие данные JSON. Обязательно замените значение **accountKey** ключом доступа вашей учетной записи хранения, который является значением, хранящимся в значении $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Затем запустите командлет New-AzureRMStreamAnalyticsInput и обязательно замените значение переменной jobDefinitionFile на путь, по которому вы сохранили JSON-файл определения входных данных задания. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Настройка выходных данных для задания

Добавьте выходные данные в задание с помощью командлета [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя выходных данных задания, имя группы ресурсов и определение выходных данных задания в качестве параметров. Определение выходных данных задания — это JSON-файл, который содержит свойства, необходимые для настройки выходных данных задания. В этом примере вы создадите хранилище BLOB-объектов в качестве выходных данных. На локальном компьютере создайте файл с именем JobOutputDefinition.json и добавьте к нему следующие данные JSON. Обязательно замените значение **accountKey** ключом доступа вашей учетной записи хранения, который является значением, хранящимся в значении $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Затем запустите командлет New-AzureRMStreamAnalyticsOutput и обязательно замените значение переменной jobOutputDefinitionFile на путь, по которому вы сохранили JSON-файл определения выходных данных задания. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Определение запроса преобразования

Добавьте преобразование в задание с помощью командлета [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя преобразования задания, имя группы ресурсов и определение преобразования задания в качестве параметров. На вашем локальном компьютере создайте файл с именем JobTransformationDefinition.json и добавьте в него следующие данные JSON: JSON-файл содержит параметр запроса, который определяет запрос преобразования:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Затем запустите командлет New-AzureRMStreamAnalyticsTransformation и обязательно замените значение переменной jobTransformationDefinitionFile на путь, по которому вы сохранили JSON-файл определения преобразования задания. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Запуск задания Stream Analytics и просмотр выходных данных

Запустите задание с помощью командлета [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя группы ресурсов, режим начала вывода задания и время запуска в качестве параметров. Параметр OutputStartMode принимает два значения (JobStartTime, CustomTime или LastOutputEventTime). Чтобы узнать, к чему относится каждое из этих значений, см. раздел [параметров](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) в документации по PowerShell. В этом примере вы можете указать режим CustomTime и предоставить значение для параметра OutputStartTime. 

Для значения времени выберите дату перед днем, когда вы передали файл в хранилище BLOB-объектов, так как файл был передан раньше текущего времени. После запуска следующего командлета, если задание запустится, будет возвращено значение True в качестве выходных данных. Контейнер с именем myoutputcontainer создается в учетной записи хранения с преобразованными данными. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, задание потоковой передачи и все связанные ресурсы можно удалить. При удалении задания будет прекращена тарификация за единицы потоковой передачи, потребляемые заданием. Если вы планируете использовать это задание в будущем, вы можете остановить и перезапустить его позже. Если вам больше не нужно это задание, удалите все ресурсы, созданные в ходе работы с этим кратким руководством, выполнив следующий командлет:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы развернули простое задание Stream Analytics. Чтобы узнать о настройке других источников входных данных и выполнить обнаружение в реальном времени, перейдите к следующей статье:

> [!div class="nextstepaction"]
> [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)

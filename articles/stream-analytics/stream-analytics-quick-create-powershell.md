---
title: Создание задания Stream Analytics с помощью Azure PowerShell
description: В этом кратком руководстве описывается, как с помощью модуля Azure PowerShell развернуть и запустить задание Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212438"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Краткое руководство по созданию задания Stream Analytics с помощью Azure PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командлетов или скриптов PowerShell. В этом кратком руководстве описывается, как с помощью модуля Azure PowerShell развернуть и запустить задание Azure Stream Analytics. 

Пример задания считывает данные потоковой передачи из большого двоичного объекта в хранилище BLOB-объектов Azure. Файл входных данных, используемый в этом кратком руководстве, содержит статические данные только для демонстрации. В реальной ситуации используйте потоковые входные данные для задания Stream Analytics. Затем задание преобразует данные с помощью языка запросов Stream Analytics, чтобы рассчитать среднюю температуру при превышении 100°. Наконец, оно записывает результирующие выходные события в другой файл. 

## <a name="before-you-begin"></a>Перед началом работы

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/).  

* Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Запустите `Get-Module -ListAvailable AzureRM`, чтобы найти версию, установленную на вашем локальном компьютере. Если вам необходимо выполнить установку или обновление, см. сведения в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в свою подписку Azure с помощью команды `Connect-AzureRmAccount` и введите учетные данные Azure во всплывающем окне браузера.

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

После входа в систему, если у вас есть несколько подписок, выберите ту, которую вы хотите использовать при работе с этим кратким руководством, запустив следующие командлеты. Не забудьте заменить <your subscription name> именем своей подписки:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Подготовка входных данных

Прежде чем определить задание Stream Analytics, подготовьте данные, которые настроены как входные данные задания.

1. Загрузите [пример данных датчика](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) с сайта GitHub. Щелкните ссылку правой кнопкой мыши и выберите пункт **Сохранить ссылку как...** или **Сохранить объект как**.

2. Следующий блок кода PowerShell выполняет несколько команд, чтобы подготовить входные данные, необходимые для задания. Просмотрите разделы, чтобы понять код. 

   1. Создайте стандартную учетную запись хранения общего назначения с помощью командлета [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  В этом примере создается учетная запись хранения mystorageaccount с включенными по умолчанию локально избыточным хранилищем (LRS) и шифрованием больших двоичных объектов.  

   2. Получите контекст учетной записи хранения `$storageAccount.Context`, определяющий необходимую учетную запись хранения. Работая в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные. 

   3. Создайте контейнер хранилища с помощью командлета [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) и передайте [пример данных датчика](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json), загруженный ранее. 

   4. Скопируйте ключ к хранилищу данных, который выводится кодом, и вставьте этот ключ в файлы JSON для последующего создания входных и выходных данных задания потоковой передачи.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

Создайте задание Stream Analytics с помощью командлета [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя группы ресурсов и определение задания в качестве параметров. Для задания можно присвоить любое понятное описательное имя. Оно может содержать только буквенно-цифровые символы, дефисы и знаки подчеркивания. Длина должна составлять от 3 до 63 символов. Определение задания — это JSON-файл, содержащий свойства, необходимые для создания задания. На вашем локальном компьютере создайте файл с именем `JobDefinition.json` и добавьте в него следующие данные JSON:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Затем выполните командлет `New-AzureRmStreamAnalyticsJob`. Не забудьте заменить значение переменной `jobDefinitionFile` путем, по которому вы сохранили JSON-файл с определением задания. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Настройка входных данных для задания

Добавьте входные данные в задание с помощью командлета [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя входных данных задания, имя группы ресурсов и определение входных данных задания в качестве параметров. Определение входных данных задания — это JSON-файл, содержащий свойства, необходимые для настройки входных данных задания. В этом примере показано, как создать хранилище BLOB-объектов в качестве входных данных. 

На вашем локальном компьютере создайте файл с именем `JobInputDefinition.json` и добавьте в него следующие данные JSON. Не забудьте заменить значение `accountKey` ключом доступа к учетной записи хранения, который являет собой значение, хранящееся в $storageAccountKey. 

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
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
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

Затем выполните командлет `New-AzureRmStreamAnalyticsInput`. Не забудьте заменить значение переменной `jobDefinitionFile` путем, по которому вы сохранили JSON-файл с определением входных данных задания. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Настройка выходных данных для задания

Добавьте выходные данные в задание с помощью командлета [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя выходных данных задания, имя группы ресурсов и определение выходных данных задания в качестве параметров. Определение выходных данных задания — это JSON-файл, содержащий свойства, необходимые для настройки выходных данных задания. В этом примере в качестве выходных данных используется хранилище BLOB-объектов. 

На вашем локальном компьютере создайте файл с именем `JobOutputDefinition.json` и добавьте в него следующие данные JSON. Не забудьте заменить значение `accountKey` ключом доступа к учетной записи хранения, который являет собой значение, хранящееся в $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
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

Затем выполните командлет `New-AzureRmStreamAnalyticsOutput`. Не забудьте заменить значение переменной `jobOutputDefinitionFile` путем, по которому вы сохранили JSON-файл с определением выходных данных задания. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Определение запроса преобразования

Добавьте преобразование в задание с помощью командлета [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя преобразования задания, имя группы ресурсов и определение преобразования задания в качестве параметров. На вашем локальном компьютере создайте файл с именем `JobTransformationDefinition.json` и добавьте в него следующие данные JSON. JSON-файл содержит параметр запроса, который определяет запрос преобразования:

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

Затем выполните командлет `New-AzureRmStreamAnalyticsTransformation`. Не забудьте заменить значение переменной `jobTransformationDefinitionFile` путем, по которому вы сохранили JSON-файл с определением преобразования задания. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Запуск задания Stream Analytics и просмотр выходных данных

Запустите задание с помощью командлета [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Этот командлет принимает имя задания, имя группы ресурсов, режим начала вывода задания и время запуска в качестве параметров. `OutputStartMode` принимает значения `JobStartTime`, `CustomTime` или `LastOutputEventTime`. Дополнительные сведения о том, к чему относится каждое из этих значений, см. в разделе [параметров](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) в документации по PowerShell. В этом примере укажите режим как `CustomTime` и укажите значение для `OutputStartTime`. 

Для значения времени выберите `2018-01-01`. Эта дата начала выбирается, так как она предшествует отметке времени для события из демонстрационных данных. После запуска следующего командлета, если задание запустится, будет возвращено значение `True` в качестве выходных данных. В контейнере хранилища будет создана выходная папка с преобразованными данными. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, задание потоковой передачи и все связанные ресурсы можно удалить. При удалении задания будет прекращена тарификация за единицы потоковой передачи, потребляемые заданием. Если вы планируете использовать задание в дальнейшем, остановите его и не выполняйте процедуру его удаления. Если вам больше не нужно это задание, удалите все ресурсы, созданные в ходе работы с этим кратким руководством, выполнив следующий командлет:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы развернули простое задание Stream Analytics. Дополнительные сведения о настройке других источников входных данных и обнаружении в режиме реального времени см. в следующей статье:

> [!div class="nextstepaction"]
> [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)

---
title: Управление Azure Data Lake Analytics с помощью Azure PowerShell | Документация Майкрософт
description: 'Узнайте, как управлять учетными записями, источниками данных, заданиями и элементами каталога Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 69530ab2ad795eaf611cb749d8c439ab07cafeac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Управление аналитикой озера данных Azure с помощью Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, заданиями и элементами каталога Azure Data Lake Analytics с помощью Azure PowerShell. 

## <a name="prerequisites"></a>предварительным требованиям

При создании учетной записи Data Lake Analytics необходимо знать следующее:

* **Идентификатор подписки** — идентификатор подписки Azure, в которую входит ваша учетная запись Data Lake Analytics.
* **Группа ресурсов** — имя группы ресурсов Azure, содержащей учетную запись Data Lake Analytics.
* **Имя учетной записи Data Lake Analytics** — имя учетной записи должно содержать только буквы в нижнем регистре и цифры.
* **Учетная запись Data Lake Store по умолчанию** — каждая учетная запись Data Lake Analytics содержит учетную запись Data Lake Store по умолчанию. Эти учетные записи должны находиться в одном расположении.
* **Расположение** — расположение учетной записи Data Lake Analytics, например "Восточная часть США 2" или другое поддерживаемое расположение. Поддерживаемые расположения можно просмотреть на [странице с расценками](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Во фрагментах кода PowerShell в этом руководстве для хранения такой информации используются следующие переменные:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Вход в систему

Вход с помощью идентификатора подписки.

```powershell
Connect-AzureRmAccount -SubscriptionId $subId
```

Вход с помощью имени подписки.

```
Connect-AzureRmAccount -SubscriptionName $subname 
```

При использовании командлета `Connect-AzureRmAccount` всегда запрашиваются учетные данные. Избежать появления запроса можно с помощью следующих командлетов:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Управление учетными записями

### <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных

Если у вас еще нет [группы ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups), создайте ее. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Для каждой учетной записи Data Lake Analytics необходима учетная запись Data Lake Store по умолчанию, которая используется для хранения журналов. Можно повторно использовать существующую учетную запись или создать новую. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

После создания группы ресурсов и учетной записи Data Lake Store создайте учетную запись Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Получение сведений об учетной записи

Получение сведений об учетной записи.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Проверка наличия конкретной учетной записи Data Lake Analytics. Командлет возвращает `$true` или `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Проверка наличия конкретной учетной записи Data Lake Store. Командлет возвращает `$true` или `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Получение списка учетных записей

Вывод списка учетных записей Data Lake Analytics в текущей подписке.

```powershell
Get-AdlAnalyticsAccount
```

Вывод списка учетных записей Data Lake Analytics в конкретной группе ресурсов.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Управление правилами брандмауэра

Вывод списка правил брандмауэра.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Добавление правила брандмауэра.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Изменение правила брандмауэра.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Удаление правила брандмауэра.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

Разрешение IP-адресов Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Управление источниками данных
Azure Data Lake Analytics в настоящее время поддерживает следующие источники данных:

* [Хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md)
* [Хранилище Azure](../storage/common/storage-introduction.md)

При создании учетной записи Analytics необходимо указать учетную запись Data Lake Store в качестве источника данных по умолчанию. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи Data Lake Analytics можно добавить дополнительные учетные записи Data Lake Store и учетные записи хранения. 

### <a name="find-the-default-data-lake-store-account"></a>Поиск учетной записи хранения озера данных по умолчанию

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Чтобы найти учетную запись по умолчанию для Data Lake Store, отфильтруйте список источников данных по свойству `IsDefault`:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Добавление источника данных

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Получение списка источников данных

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Отправка заданий U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Отправка строки как скрипта U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-script"></a>Отправка файла как скрипта U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Получение списка заданий в учетной записи

### <a name="list-all-the-jobs-in-the-account"></a>Откройте список всех заданий в учетной записи. 

Результаты включают в себя текущие и недавно завершенные задания.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Получение списка последних N заданий

По умолчанию список заданий сортируется по времени отправки. Поэтому в его начале находятся последние отправленные задания. По умолчанию в учетной записи ADLA сохраняются сведения о заданиях за 180 дней, но командлет Ge-AdlJob по умолчанию возвращает только первые 500 заданий. Чтобы получить список определенного количества заданий, используйте параметр -Top.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Получение списка заданий на основе значения свойства задания

Использование параметра `-State`. Вы можете использовать любое сочетание следующих значений:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Используйте параметр `-Result`, чтобы определить, успешно ли выполнено завершенное задание. Возможны следующие значения:

* Отменено
* Сбой
* Нет
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

Параметр `-Submitter` позволяет определить, кто отправил это задание.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

Параметр `-SubmittedAfter` используется для фильтрации по диапазону времени.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Анализ журнала заданий

Использование Azure PowerShell для анализа журнала заданий, выполненных в Azure Data Lake является эффективным методом. Это удобный инструмент для анализа использования и затрат. Дополнительные сведения см. в [примере репозитория для выполнения анализа журнала заданий](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Получение сведений о конвейерах и повторениях

Используйте командлет `Get-AdlJobPipeline`, чтобы получить сведения о конвейерах для ранее отправленных заданий.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Используйте командлет `Get-AdlJobRecurrence`, чтобы получить сведения о повторениях для ранее отправленных заданий.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Получение информации о задании

### <a name="get-job-status"></a>Получение состояния задания

Получите состояние конкретного задания.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Изучение выходных данных задания

По завершении задания проверьте, существует ли выходной файл, открыв список файлов в папке.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Управление выполнением заданий

### <a name="cancel-a-job"></a>Отмена задания

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Ожидание завершения задания

Вместо того чтобы повторно выполнять `Get-AdlAnalyticsJob`, пока задание не завершится, можно использовать командлет `Wait-AdlJob`, чтобы дождаться завершения задания.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Управление политиками вычислений

### <a name="list-existing-compute-policies"></a>Список существующих политик вычислений

Командлет `Get-AdlAnalyticsComputePolicy` извлекает информацию о политиках вычислений для учетной записи Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Создание политики вычислений

Командлет `New-AdlAnalyticsComputePolicy` создает новую политику вычислений для учетной записи Data Lake Analytics. Этот пример устанавливает для указанного пользователя максимальное количество единиц аналитики (50) и минимальный приоритет задания (250).

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Проверьте наличие файла.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Отправка и скачивание

Отправка файла.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Рекурсивная отправка всей папки.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Скачивание файла.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Рекурсивное скачивание всей папки.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Если процесс отправки или скачивания прервался, вы можете попытаться возобновить его, выполнив командлет еще раз с флагом ``-Resume``.

## <a name="manage-catalog-items"></a>Управление элементами каталога

Каталог U-SQL используется для структурирования данных и кода, чтобы их могли совместно использовать сценарии U-SQL. Каталог обеспечивает максимальную производительность, возможную с данными в озере данных Azure. Дополнительные сведения см. в разделе [Использование каталога U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Получение списка элементов в каталоге U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Получение списка всех сборок во всех базах данных в учетной записи ADLA.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Получение сведений об элементе каталога

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Создание учетных данных в каталоге

В базе данных U-SQL создайте объект учетных данных для базы данных, размещенной в Azure. В настоящее время учетные данные U-SQL — это единственный тип элементов каталога, который можно создавать с помощью PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>Получение основных сведений об учетной записи ADLA

Приведенный ниже код выполняет поиск основных сведений об учетной записи, указанной по имени.

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Работа с Azure

### <a name="get-details-of-azurerm-errors"></a>Получение сведений об ошибках AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Проверка выполнения с правами администратора

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Поиск TenantID

По имени подписки:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

По идентификатору подписки:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

По адресу домена, например contoso.com:


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Получение списка всех подписок и идентификаторов клиентов

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Создание учетной записи Data Lake Analytics с помощью шаблона

Вы также можете использовать шаблон группы ресурсов Azure с помощью следующего скрипта PowerShell:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Connect-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Дополнительные сведения см. в разделах [Deploy an application with Azure Resource Manager template](../azure-resource-manager/resource-group-template-deploy.md) (Развертывание приложения с использованием шаблона Azure Resource Manager) и [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Пример шаблона**

Сохраните приведенный ниже текст в файле `.json`, а затем воспользуйтесь предыдущим скриптом PowerShell для применения шаблона. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Дополнительная информация
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* Начало работы с Data Lake Analytics с помощью [портала Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Управление Azure Data Lake Analytics с помощью [портала Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 

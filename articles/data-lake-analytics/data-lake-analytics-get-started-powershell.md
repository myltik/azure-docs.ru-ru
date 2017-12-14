---
title: "Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell | Документация Майкрософт"
description: "Используйте Azure PowerShell для создания учетной записи Data Lake Analytics, создания задания Data Lake Analytics с помощью U-SQL и его отправки. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.openlocfilehash: 832a859e70e382eb2eeb41560d1b880f7b87de53
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как использовать Azure PowerShell для создания учетных записей Azure Data Lake Analytics, а затем отправлять и выполнять задания U-SQL. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством необходимо иметь следующую информацию:

* **Учетная запись Azure Data Lake Analytics**. См. раздел [Начало работы с Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Рабочая станция с Azure PowerShell**. См. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Вход в Azure

Для работы с руководством необходим опыт работы с Azure PowerShell. В частности вам нужно знать, как выполнить вход в Azure. Если вам нужна помощь, см. раздел [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Вход в систему с использованием имени подписки:

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Вместо имени подписки для входа можно использовать ее идентификатор:

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

При успешном выполнении этой команды выходные данные будут выглядеть так:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Подготовка к работе с руководством

В этом руководстве во фрагментах кода PowerShell для хранения информации используются следующие переменные:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Получение сведений об учетной записи Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Отправка задания U-SQL

Создайте переменную для хранения скрипта U-SQL PowerShell.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Отправьте скрипт.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" –Script $script
```

Или же можно сохранить скрипт в файл, а затем отправить его с помощью следующей команды:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" –ScriptPath $filename
```


Получите состояние конкретного задания. Продолжайте использовать этот командлет, пока задание не будет выполнено.

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Вместо того чтобы снова и снова вызывать командлет Get-AdlAnalyticsJob, пока задание не будет завершено, можно использовать командлет Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Загрузите выходной файл.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>См. также
* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).

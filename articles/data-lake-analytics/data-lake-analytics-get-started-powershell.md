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
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Руководство. Начало работы с аналитикой озера данных Azure с помощью Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как использовать Azure PowerShell для создания учетных записей Azure Data Lake Analytics, а затем отправлять и выполнять задания U-SQL. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством необходимо иметь следующую информацию:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* <seg>
  **Рабочая станция с Azure PowerShell**.</seg> См. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Подготовка к работе с руководством
Для создания учетной записи Data Lake Analytics сперва необходимо указать следующее.

* **Группа ресурсов Azure**. В группе ресурсов Azure необходимо создать учетную запись Data Lake Analytics.
* **Имя учетной записи Data Lake Analytics**. Имя учетной записи Data Lake должно содержать только буквы в нижнем регистре и цифры.
* **Расположение**: один из центров обработки данных Azure, который поддерживает аналитику озера данных.
* **Учетная запись Data Lake Store по умолчанию**. Каждая учетная запись Data Lake Analytics содержит учетную запись Data Lake Store по умолчанию. Эти учетные записи должны находиться в одном расположении.

Во фрагментах кода PowerShell в этом руководстве для хранения такой информации используются следующие переменные:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных

Если у вас еще нет группы ресурсов, создайте ее. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Для каждой учетной записи Data Lake Analytics необходима учетная запись Data Lake Store по умолчанию, которая используется для хранения журналов. Можно повторно использовать существующую учетную запись или создать новую. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

После создания группы ресурсов и учетной записи Data Lake Store создайте учетную запись Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Получение сведений об учетной записи Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Отправка задания U-SQL

Создайте текстовый файл со следующим скриптом U-SQL:

```
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
```

Отправьте скрипт.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Мониторинг заданий U-SQL

Откройте список всех заданий в учетной записи. Результаты включают в себя текущие и недавно завершенные задания.

```
Get-AdlJob -Account $adla
```

Получите состояние конкретного задания.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Вместо того чтобы снова и снова вызывать командлет Get-AdlAnalyticsJob, пока задание не будет завершено, можно использовать командлет Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

По завершении задания проверьте, существует ли выходной файл, открыв список файлов в папке.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Проверьте наличие файла.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Отправка и скачивание файлов

Скачайте выходные данные скрипта U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Отправьте файл, который необходимо использовать в качестве входных данных для скрипта U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>См. также
* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).


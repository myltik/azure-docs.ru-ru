---
title: "Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell | Документация Майкрософт"
description: "Узнайте, как использовать Azure PowerShell для создания учетной записи хранилища озера данных, создания задания аналитики озера данных с помощью U-SQL и отправки задания. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 59efa050944059c737654a3f039a058c50865ea6


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Руководство. Начало работы с аналитикой озера данных Azure с помощью Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как использовать Azure PowerShell для создания учетных записей Azure Data Lake Analytics, определения заданий Data Lake Analytics в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправки заданий в учетные записи Data Lake Analytic. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

В этом учебнике вам предстоит разработать задание, которое считывает файл с разделителями-табуляциями (TSV) и преобразует его в файл с разделителями-запятыми (CSV). Для навигации по учебнику с помощью других поддерживаемых средств используйте вкладки в верхней части этого раздела.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Рабочая станция с Azure PowerShell.**. См. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## <a name="create-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
Для выполнения любых заданий требуется учетная запись аналитики озера данных. Для создания учетной записи аналитики озера данных необходимо указать следующее.

* **Группа ресурсов Azure**: необходимо создать учетную запись аналитики озера данных в группе «Ресурс Azure». [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) позволяет работать с группами ресурсов в приложении. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции.  

    Вот как перечислить группы ресурсов в своей подписке.

        Get-AzureRmResourceGroup

    Чтобы создать новую группу ресурсов:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Имя учетной записи аналитики озера данных**
* **Расположение**: один из центров обработки данных Azure, который поддерживает аналитику озера данных.
* **Учетная запись озера данных по умолчанию**: у каждой учетной записи аналитики озера данных есть учетная запись озера данных по умолчанию.

    Создание новой учетной записи озера данных

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

  > [!NOTE]
  > Имя учетной записи озера данных должно содержать только буквы нижнего регистра и цифры.
  >
  >

**Создание учетной записи аналитики озера данных**

1. Откройте PowerShell ISE на своей рабочей станции Windows.
2. Выполните следующий скрипт:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"

        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location

        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location

        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName

        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

## <a name="upload-data-to-data-lake"></a>Отправка данных в озеро данных
В этом учебнике обрабатываются некоторые журналы поиска.  Журнал поиска может храниться в хранилище озера данных или в хранилище больших двоичных объектов Azure.

Пример файла журнала поиска был скопирован в общедоступный контейнер больших двоичных объектов Azure. Воспользуйтесь следующим скриптом PowerShell для загрузки файла на свою рабочую станцию, а затем отправьте файл в учетную запись хранилища озера данных по умолчанию своей учетной записи аналитики озера данных.

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location    
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Следующий скрипт PowerShell позволяет получить имя хранилища озера данных по умолчанию для учетной записи аналитики озера данных:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

> [!NOTE]
> На портале Azure доступен пользовательский интерфейс для копирования примеров файлов данных в учетную запись хранилища озера данных по умолчанию. См. инструкции в статье о [начале работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md#prepare-source-data).
>
>

Из аналитики озера данных также доступно хранилище больших двоичных объектов Azure.  Инструкции по отправке данных в хранилище BLOB-объектов Azure см. в статье [Использование Azure PowerShell со службой хранилища Azure](../storage/storage-powershell-guide-full.md).

## <a name="submit-data-lake-analytics-jobs"></a>Отправка заданий аналитики озера данных
Задания аналитики озера данных пишутся на языке U-SQL. Дополнительные сведения о языке U-SQL см. в статье о [начале работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Создание скрипта задания аналитики озера данных**

* Создайте текстовый файл со следующим скриптом U-SQL, а затем сохраните текстовый файл на своей рабочей станции.

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Этот сценарий U-SQL считывает файл исходных данных с помощью **Extractors.Tsv()**, а затем создает CSV-файл с помощью **Outputters.Csv()**.

    Не меняйте эти два пути, если только исходный файл не был скопирован в другое место.  Аналитика озера данных создаст выходную папку, если ее не существует.

    Проще использовать относительные пути для файлов, которые хранятся в учетных записях озера данных по умолчанию. Также можно использовать абсолютные пути.  Например:

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Для доступа к файлам в связанных учетных записях хранения используйте абсолютные пути.  Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Контейнер больших двоичных объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам в настоящее время не поддерживается.    
  >
  >

**Отправка задания**

1. Откройте PowerShell ISE на своей рабочей станции Windows.
2. Выполните следующий скрипт:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"

        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    В скрипте файл скрипта U-SQL сохранен в папке С:\tutorials\data-lake-analytics\copyFile.usql. Соответствующим образом обновите путь к файлу.

По окончании задания используйте следующие командлеты, чтобы включить файл в список, а затем загрузите файл.

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Дополнительные материалы
* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в статье [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).



<!--HONumber=Nov16_HO2-->



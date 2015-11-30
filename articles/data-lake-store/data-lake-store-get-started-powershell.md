<properties 
   pageTitle="Начало работы с хранилищем озера данных | Azure" 
   description="Использование Azure PowerShell для создания учетной записи хранения озера данных и выполнения базовых операций" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

Узнайте, как с помощью Azure PowerShell создать учетную запись хранения озера данных Azure и выполнять базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения о хранилище озера данных см. в статье [Обзор хранилища озера данных](data-lake-store-overview.md).

## Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
- **Azure PowerShell версии 1.0 или выше**. Инструкции см. в статье [Установка и настройка Azure PowerShell](../install-configure-powershell.md).

## Создание учетной записи хранения озера данных Azure

1. На рабочем столе откройте новое окно Azure PowerShell и выполните следующий фрагмент кода, чтобы войти в свою учетную запись Azure, задать подписку и зарегистрировать поставщик хранилища озера данных. При появлении запроса на вход введите учетные данные администратора или владельца подписки.

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>
        
		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore" 


2. Учетная запись хранения озера данных Azure связывается с группой ресурсов Azure. Для начала создайте группу ресурсов Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Создание группы ресурсов Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Создание группы ресурсов Azure")

2. Создайте учетную запись хранения озера данных Azure. Указанное имя должно содержать только строчные буквы и цифры.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Создание учетной записи хранения озера данных Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Создание учетной записи хранения озера данных Azure")

3. Убедитесь, что учетная запись создана.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Должен быть возвращен результат **True**.

## Создание структуры каталогов в хранилище озера данных Azure

Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранения озера данных Azure.

1. Укажите корневой каталог.

		$myrootdir = "/"

2. Создайте новый каталог с именем **mynewdirectory** в указанном корневом каталоге.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Убедитесь, что новый каталог создан.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	Вы должны увидеть примерно следующие выходные данные:

	![Проверка каталога](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Проверка каталога")


## Передача данных в хранилище озера данных Azure

Данные можно передавать в хранилище озера данных Azure непосредственно на корневой уровень или в каталог, созданный в учетной записи. Фрагменты кода ниже показывают, как передать некоторые образцы данных в каталог (**mynewdirectory**), который был создан в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData). Загрузите файл и сохраните его в локальном каталоге на компьютере, например C:\\sampledata.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Переименование, загрузка и удаление данных из хранилища озера данных

Чтобы переименовать файл, используйте следующую команду:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Чтобы загрузить файл, используйте следующую команду:

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Чтобы удалить файл, используйте следующую команду:

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
	
При появлении запроса введите **Y**, чтобы удалить элемент. Если нужно удалить несколько файлов, можно указать все пути через запятую.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Удаление учетной записи хранения озера данных Azure

Чтобы удалить свою учетную запись хранения озера данных, используйте следующую команду.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

При появлении запроса введите **Y**, чтобы удалить учетную запись.


## Другие способы создания учетной записи хранения озера данных

- [Начало работы с хранилищем озера данных с помощью портала](data-lake-store-get-started-portal.md)
- [Начало работы с хранилищем озера данных с помощью пакета SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Начало работы с хранилищем озера данных с помощью Azure CLI](data-lake-store-get-started-cli.md)


## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=Nov15_HO4-->
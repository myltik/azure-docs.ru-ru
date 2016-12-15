---
title: "Начало работы с Data Lake Store | Документация Майкрософт"
description: "Использование Azure PowerShell для создания учетной записи хранения озера данных и выполнения базовых операций"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a86fd04a7ec0cffabe42d30132b97777c752bbde
ms.openlocfilehash: e37b698436c067faa20b0e589078927d5955934a


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Начало работы с хранилищем озера данных Azure с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
> * [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Узнайте, как с помощью Azure PowerShell создать учетную запись хранения озера данных Azure и выполнять базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения о хранилище озера данных см. в [обзоре Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 или более поздней версии**. См. статью [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="authentication"></a>Аутентификация
В этой статье используется более простой подход к проверке подлинности в Data Lake Store, при котором нужно ввести учетные данные учетной записи Azure. Уровень доступа к учетной записи Data Lake Store и файловой системе зависит от уровня доступа пользователя, который вошел в систему. Существуют разные способы проверки подлинности в Data Lake Store, включая **проверку подлинности пользователя** и **проверку подлинности с взаимодействием между службами**. Инструкции и дополнительные сведения о проверке подлинности см. в статье, посвященной [проверке подлинности приложений Data Lake Store с помощью Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Создание учетной записи хранения озера данных Azure
1. На рабочем столе откройте новое окно Windows PowerShell и выполните следующий фрагмент кода, чтобы войти в свою учетную запись Azure, выбрать подписку и зарегистрировать поставщик Data Lake Store. Когда вам будет предложено войти, введите учетные данные администратора или владельца подписки.

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
3. Создайте учетную запись хранения озера данных Azure. Указанное имя должно содержать только строчные буквы и цифры.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Создание учетной записи Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Создание учетной записи Azure Data Lake Store")
4. Убедитесь, что учетная запись создана.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Результат должен иметь значение **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Создание структуры каталогов в хранилище озера данных Azure
Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранения озера данных Azure.

1. Укажите корневой каталог.

        $myrootdir = "/"
2. Создайте новый каталог с именем **mynewdirectory** в указанном корневом каталоге.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Убедитесь, что новый каталог создан.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Вы должны увидеть примерно следующие выходные данные:

    ![Проверка каталога](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Проверка каталога")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Передача данных в хранилище озера данных Azure
Данные можно передавать в хранилище озера данных Azure непосредственно на корневой уровень или в каталог, созданный в учетной записи. Фрагменты кода ниже показывают, как отправить некоторые образцы данных в каталог (**mynewdirectory**), который был создан в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Скачайте файл и сохраните его в локальном каталоге на компьютере, например C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Переименование, загрузка и удаление данных из хранилища озера данных
Чтобы переименовать файл, используйте следующую команду:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Чтобы загрузить файл, используйте следующую команду:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Чтобы удалить файл, используйте следующую команду:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

При появлении запроса введите **Y** , чтобы удалить элемент. Если нужно удалить несколько файлов, можно указать все пути через запятую.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Удаление учетной записи хранения озера данных Azure
Чтобы удалить свою учетную запись хранения озера данных, используйте следующую команду.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

При появлении запроса введите **Y** , чтобы удалить учетную запись.

## <a name="performance-guidance-while-using-powershell"></a>Рекомендации по производительности при использовании PowerShell

Ниже приведены самые важные параметры, настроив которые можно обеспечить высокую производительность при использовании PowerShell для работы с Data Lake Store:

| Свойство            | значение по умолчанию | Описание |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Этот параметр позволяет выбрать количество параллельных потоков для отправки или скачивания каждого файла. Это количество представляет собой максимальное количество потоков, которые можно выделить для каждого файла. В зависимости от сценария количество потоков может быть меньше (например, при отправке файла размером 1 КБ вы получите один поток, даже если запросите 20).  |
| ConcurrentFileCount | 10      | Этот параметр предназначен для отправки или скачивания папок. Он определяет количество одновременно отправляемых или скачиваемых файлов. Это количество представляет собой максимальное количество файлов, которые можно отправить или скачать одновременно. В зависимости от сценария количество параллельно отправляемых или скачиваемых файлов может быть меньше (например, при отправке двух файлов одновременно будут отправляться два файла, даже если запрошено 15). |

**Пример**

Эта команда скачивает файлы из Azure Data Lake Store на локальный диск пользователя, используя 20 потоков на один файл и 100 одновременно скачиваемых файлов.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Как определить значение для этих параметров?

Ниже представлены некоторые полезные рекомендации.

* **Шаг 1. Определение общего числа потоков.** Сначала нужно определить общее число потоков, которые следует использовать. Как правило, следует использовать 6 потоков для каждого физического ядра.

        Total thread count = total physical cores * 6

    **Пример**

    Предположим, что вы выполняете команды PowerShell на виртуальной машине D14 с 16 ядрами.

        Total thread count = 16 cores * 6 = 96 threads


* **Шаг 2. Расчет значения параметра PerFileThreadCount.** Мы вычисляем значение параметра PerFileThreadCount на основе размера файлов. Для файлов размером менее 2,5 ГБ не нужно изменять этот параметр, так как значения по умолчанию 10 будет достаточно. Для файлов размером более 2,5 ГБ следует использовать 10 потоков для первых 2,5 ГБ и добавлять по 1 потоку на каждые дополнительные 256 МБ файла. При копировании папки с файлами разного размера рекомендуется разделить их на группы по схожим размерам. Использование файлов разных размеров может привести к снижению производительности. Если невозможно сгруппировать файлы по схожим размерам, следует задать значение параметра PerFileThreadCount в соответствии с максимальным размером файла.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Пример**

    Предположим, что у вас есть 100 файлов от 1 до 10 ГБ. Мы используем файл на 10 ГБ в качестве максимального размера файла в формуле, подобной следующей.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Шаг 3. Вычисление значения параметра ConcurrentFilecount.** Используйте общее число потоков и значение параметра PerFileThreadCount, чтобы вычислить значение параметра ConcurrentFileCount по следующей формуле.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Пример**

    На основе использованных примеров значений.

        96 = 40 * ConcurrentFileCount

    Таким образом значение **ConcurrentFileCount** — **2,4**, которое можно округлить до **2**.

### <a name="further-tuning"></a>Дополнительные настройки

Вам могут потребоваться дополнительные настройки, так как вы работаете с файлами разного размера. Вычисления выше подходят, если размер всех или большинства файлов больше 10 ГБ или ближе к этому показателю. Однако если у вас есть множество файлов разных размеров (меньше 10 ГБ), можно уменьшить значение параметра PerFileThreadCount. Сделав так, мы увеличим значение ConcurrentFileCount. Таким образом, если предположить, что большая часть файлов меньше 5 ГБ, можно повторно выполнить вычисления:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Теперь **ConcurrentFileCount** имеет значение 96/20 — 4,8, которое можно округлить до **4**.

Вы можете продолжить изменять значения этих параметров, уменьшая и увеличивая значение **PerFileThreadCount** в зависимости от размера файлов.

### <a name="limitation"></a>Ограничение

* **Число файлов меньше, чем значение ConcurrentFileCount.** Если количество отправляемых файлов меньше вычисленного значения **ConcurrentFileCount**, следует уменьшить значение **ConcurrentFileCount** в соответствии с числом файлов. Все остальные потоки можно использовать для увеличения значения **PerFileThreadCount**.

* **Слишком много потоков.** Указав слишком большое число потоков, не увеличивая размер кластера, вы рискуете снизить производительность. При переключении контекста в ЦП могут возникнуть конфликты.

* **Недостаточный уровень параллелизма.** Если уровень параллелизма недостаточный, кластер может быть слишком мал. Вы можете увеличить количество узлов в кластере, что повысит уровень параллелизма.

* **Ошибки регулирования.** При слишком высоком уровне параллелизма могут возникнуть ошибки регулирования. При этом необходимо уменьшить уровень параллелизма или обратиться к нам.

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Dec16_HO2-->



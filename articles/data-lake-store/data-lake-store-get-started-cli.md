---
title: "Начало работы с Data Lake Store с помощью кроссплатформенного интерфейса командной строки | Документация Майкрософт"
description: "Использование кроссплатформенного интерфейса командной строки Azure для создания учетной записи хранилища озера данных и выполнения базовых операций."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2449f00e298385579c4d7b229ceea18dcc598


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Приступая к работе с хранилищем озера данных Azure с помощью командной строки Azure
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
> * [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Узнайте, как с помощью интерфейса командной строки Azure создать учетную запись хранилища озера данных Azure и выполнять базовые операции, такие как создание папок, передача и скачивание файлов данных, удаление учетной записи и т. д. Дополнительные сведения о Data Lake Store см. в [обзоре Data Lake Store](data-lake-store-overview.md).

Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Azure CLI имеет открытый исходный код. Исходный код управляется с помощью GitHub по адресу <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. В этой статье описывается только использование Azure CLI с хранилищем озера данных. Общее руководство по использованию CLI Azure см. в статье [Установка Azure CLI][azure-command-line-tools].

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Интерфейс командной строки Azure CLI** — сведения об установке и настройке CLI Azure см. в статье [Установка Azure CLI](../xplat-cli-install.md). Обязательно перезагрузите компьютер после установки интерфейса командной строки.

## <a name="authentication"></a>Аутентификация
В этой статье используется более простой подход к проверке подлинности в службе Data Lake Store, в которую выполняется вход от имени пользователя. Уровень доступа к учетной записи Data Lake Store и файловой системе зависит от уровня доступа пользователя, который вошел в систему. Существуют разные способы проверки подлинности в Data Lake Store, включая **проверку подлинности пользователя** и **проверку подлинности с взаимодействием между службами**. Инструкции и дополнительные сведения о проверке подлинности см. в статье, посвященной [проверке подлинности приложений Data Lake Store с помощью Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="login-to-your-azure-subscription"></a>Вход в подписку Azure
1. Выполните действия, описанные в статье, посвященной [подключению к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md), и подключитесь к подписке с помощью метода `azure login`.
2. Перечислите подписки, связанные с учетной записью, используя команду `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    В выходных данных выше видно, что доступны подписка **Azure-sub-1**, которая сейчас включена, и подписка **Azure-sub-2**. 
3. Выберите подписку, которую вы хотите использовать. Если вы хотите использовать подписку Azure-sub-2, выполните команду `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Создание учетной записи хранения озера данных Azure
Откройте командную строку, оболочку или сеанс терминала и выполните следующие команды.

1. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:
   
        azure config mode arm
2. Создайте новую группу ресурсов. В следующей команде укажите значения параметров, которые требуется использовать.
   
        azure group create <resourceGroup> <location>
   
    Если имя расположения содержит пробелы, заключите его в кавычки. Например, "East US 2".
3. Создайте учетную запись хранилища озера данных.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Создание папок в хранилище озера данных
Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранилища озера данных Azure. Используйте следующую команду, чтобы создать папку с именем "mynewfolder" в корневом каталоге хранилища озера данных.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Например:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Передача данных в хранилище озера данных
Данные можно передавать в хранилище озера данных Azure непосредственно на корневой уровень или в папку, созданную в учетной записи. Фрагменты кода ниже показывают, как передать некоторые примеры данных в папку (**mynewfolder**), которая была создана в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Скачайте файл и сохраните его в локальном каталоге на компьютере, например C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Например:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Вывод списка файлов в хранилище озера данных
Чтобы вывести список файлов в учетной записи хранилища озера данных, используйте следующую команду.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Например:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

Результат этой команды должен выглядеть примерно так:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Переименование, загрузка и удаление данных из хранилища озера данных
* **Чтобы переименовать файл**, используйте следующую команду:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Например:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Чтобы загрузить файл**, используйте следующую команду. Убедитесь, что указанный конечный путь уже существует.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Например:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Чтобы удалить файл**, используйте следующую команду:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Например:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    При появлении запроса введите **Y** , чтобы удалить элемент.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Просмотр списка управления доступом для папки в хранилище озера данных
Используйте следующую команду для просмотра списков ACL для папки хранилища озера данных. В текущем выпуске списки ACL можно задать только в корневом каталоге хранилища озера данных. Таким образом, приведенный ниже параметр пути всегда будет указывать на корень (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Например:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Удаление учетной записи хранилища озера данных
Чтобы удалить учетную запись хранилища озера данных, используйте следующую команду.

    azure datalake store account delete <dataLakeStoreAccountName>

Например:

    azure datalake store account delete mynewdatalakestore

При появлении запроса введите **Y** , чтобы удалить учетную запись.

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Nov16_HO2-->



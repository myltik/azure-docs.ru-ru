---
title: "Использование Azure Data Lake Store с помощью интерфейса командной строки Azure CLI 2.0 | Документация Майкрософт"
description: "Использование кроссплатформенного интерфейса командной строки Azure 2.0 для создания учетной записи Data Lake Store и выполнения базовых операций"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: c5971a137d9081be8c5978f481ec42a1f91e5a56
ms.contentlocale: ru-ru
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Начало работы с Azure Data Lake Store с помощью Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Узнайте, как с помощью Azure CLI 2.0 создать учетную запись Azure Data Lake Store и выполнять такие базовые операции, как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения о Data Lake Store см. в [обзоре Data Lake Store](data-lake-store-overview.md).

Azure CLI 2.0 — это новый интерфейс командной строки Azure для управления ресурсами Azure. Его можно использовать в Windows, Linux и macOS. Дополнительные сведения см. в [обзоре Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview). Полный список команд и синтаксис см. в [справочнике интерфейса командной строки 2.0 Azure Data Lake Store](https://docs.microsoft.com/cli/azure/dls).


## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI 2.0** — см. инструкции в статье об [установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="authentication"></a>Аутентификация

В этой статье используется более простой подход к проверке подлинности в службе Data Lake Store, в которую выполняется вход от имени пользователя. Уровень доступа к учетной записи Data Lake Store и файловой системе зависит от уровня доступа пользователя, который вошел в систему. Существуют разные способы проверки подлинности в Data Lake Store, включая **проверку подлинности пользователя** и **проверку подлинности с взаимодействием между службами**. Инструкции и дополнительные сведения об аутентификации см. в разделах [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md) и [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Вход в подписку Azure

1. Войдите в подписку Azure.

    ```azurecli
    az login
    ```

    Получите код для использования на следующем шаге. Откройте в веб-браузере страницу https://aka.ms/devicelogin и введите код для аутентификации. Вам будет предложено выполнить вход с использованием учетных данных.

2. После входа вы увидите список всех подписок Azure, связанных с вашей учетной записи. Чтобы выбрать определенную подписку, выполните следующую команду.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Создание учетной записи хранения озера данных Azure

1. Создайте новую группу ресурсов. В следующей команде укажите значения параметров, которые требуется использовать. Если имя расположения содержит пробелы, заключите его в кавычки. Например, "East US 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Создайте учетную запись хранилища озера данных.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Создание папок в учетной записи хранения озера данных

Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи хранилища озера данных Azure. Используйте следующую команду, чтобы создать папку с именем **mynewfolder** в корневом каталоге Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> Команда создаст папку, используя параметр `--folder`. Если этот параметр отсутствует, команда создаст пустой файл с именем mynewfolder в корневом каталоге учетной записи Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Отправка данных в учетную запись Data Lake Store

Данные можно передавать в Data Lake Store непосредственно на корневой уровень или в папку, созданную в учетной записи. Фрагменты кода ниже показывают, как передать некоторые примеры данных в папку (**mynewfolder**), которая была создана в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Загрузите файл и сохраните его в локальном каталоге на компьютере, например в расположении C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Укажите полный путь в качестве назначения, включая имя файла.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Вывод списка файлов в учетной записи Data Lake Store

Чтобы вывести список файлов в учетной записи хранилища озера данных, используйте следующую команду.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

Результат этой команды должен выглядеть примерно так:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Переименование, скачивание и удаление данных из Data Lake Store 

* **Чтобы переименовать файл**, используйте следующую команду:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Чтобы загрузить файл**, используйте следующую команду. Убедитесь, что указанный конечный путь уже существует.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Команда создаст целевую папку, если она не существует.
    > 
    >

* **Чтобы удалить файл**, используйте следующую команду:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Если вы хотите удалить папку **mynewfolder** и файл **vehicle1_09142014_copy.csv** с помощью одной команды, используйте параметр --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Работа с разрешениями и списками управления доступом для учетной записи Data Lake Store

Из этого раздела вы узнаете, как управлять списками управления доступом и разрешениями, с помощью Azure CLI 2.0. Подробные сведения о реализации списков ACL в Azure Data Lake Store см. в статье [Контроль доступа в Azure Data Lake Store](data-lake-store-access-control.md).

* **Чтобы обновить владельца файла или папки**, используйте следующую команду:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Чтобы обновить разрешения для файла или папки**, используйте следующую команду:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Чтобы получить списки ACL для определенного пути**, используйте следующую команду:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    Должен быть получен результат, аналогичный приведенному ниже:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Чтобы создать запись для ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Чтобы удалить запись для ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Чтобы удалить весь стандартный список ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Чтобы удалить весь настраиваемый список ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Удаление учетной записи хранения озера данных
Чтобы удалить учетную запись хранилища озера данных, используйте следующую команду.

```azurecli
az dls account delete --account mydatalakestore
```

При появлении запроса введите **Y** , чтобы удалить учетную запись.

## <a name="next-steps"></a>Дальнейшие действия

* [Справочник по CLI 2.0 Azure Data Lake](https://docs.microsoft.com/cli/azure/dls)
* [Защита данных в Data Lake Store](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md


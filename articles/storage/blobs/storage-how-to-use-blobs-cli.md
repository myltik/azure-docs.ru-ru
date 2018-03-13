---
title: "Выполнение операций в хранилище BLOB-объектов Azure (хранилище объектов) с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как отправлять и скачивать большие двоичные объекты в хранилище BLOB-объектов Azure, а также как создать подписанный URL-адрес (SAS), чтобы управлять доступом к большому двоичному объекту в учетной записи хранения."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: 4f781508652ee784fff61c8ecc76cb87ea47f0c2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Выполнение операций в хранилище BLOB-объектов с помощью Azure CLI

Хранилище BLOB-объектов Azure — это служба хранения большого количества неструктурированных данных объектов, таких как текстовые или двоичные данные, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. В этой статье описываются основные операции в хранилище BLOB-объектов Azure, например отправка, скачивание и удаление больших двоичных объектов. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание контейнера
> * отправка файла (большого двоичного объекта) в контейнер;
> * Перечисление BLOB-объектов в контейнере
> * Скачивание большого двоичного объекта из контейнера
> * копирование большого двоичного объекта между учетными записями хранения;
> * Удаление большого двоичного объекта
> * отображение и изменение свойств большого двоичного объекта и метаданных;
> * управление безопасностью с помощью подписанного URL-адреса (SAS).

Для работы с этим руководством вам потребуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Создание контейнера

Контейнеры аналогичны каталогам на компьютере. Они позволяют структурировать группы больших двоичных объектов в контейнере подобно упорядочиванию файлов в каталоге. Учетная запись хранения может содержать любое количество контейнеров. Вы можете хранить в контейнере до 500 ТБ данных больших двоичных объектов. Это максимальный объем данных в учетной записи хранения.

Создайте контейнер для хранения больших двоичных объектов с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Имена контейнеров должны начинаться с буквы или цифры и могут содержать только буквы, цифры и знак дефиса (-). Дополнительные сведения об именовании больших двоичных объектов и контейнеров см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

## <a name="enable-public-read-access-for-a-container"></a>Активация общего доступа на чтение к контейнеру

Созданный контейнер является закрытым по умолчанию. Это значит, что никто не может получить доступ к контейнеру без [подписанного URL-адреса](#create-a-shared-access-signature-sas) или ключей доступа для учетной записи хранения. С помощью Azure CLI можно изменить это поведение, задав для разрешений контейнера один из трех уровней.

| | |
|---|---|
| `--public-access off` | Без общего доступа на чтение (по умолчанию) |
| `--public-access blob` | Общий доступ на чтение только больших двоичных объектов |
| `--public-access container` | Общий доступ на чтение больших двоичных объектов, можно перечислить большие двоичные объекты в контейнере |

Если задать общий доступ к `blob` или `container`, вы предоставите доступ только на чтение любому пользователю в Интернете. Например, если вы хотите отобразить изображения, хранимые в виде больших двоичных объектов на веб-сайте, необходимо включить общий доступ на чтение. Если вы хотите разрешить доступ на чтение и запись, необходимо использовать [подписанный URL-адрес (SAS)](#create-a-shared-access-signature-sas).

Активируйте общий доступ на чтение для контейнера с помощью команды [az storage container set-permission](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Отправка большого двоичного объекта в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Блочные BLOB-объекты — это наиболее распространенный тип больших двоичных объектов в службе хранилища Azure. Добавочные BLOB-объекты используются, когда данные необходимо добавить в имеющийся большой двоичный объект без изменения его текущего содержимого, например для ведения журнала. Страничные BLOB-объекты содержат файлы VHD виртуальных машин IaaS.

В этом примере мы отправим большой двоичный объект в контейнер, созданный на последнем шаге, с помощью команды [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

С помощью этой операции создается большой двоичный объект, если он не был создан ранее, или же, если он имеется, происходит его замещение. Отправьте несколько файлов, чтобы увидеть несколько записей при получении списка больших двоичных объектов на следующем шаге.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Выведите список больших двоичных объектов в контейнере, выполнив команду [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Пример выходных данных:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Загрузка BLOB-объектов

Загрузите большой двоичный объект, который вы отправили на предыдущем шаге, выполнив команду [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>копирование большого двоичного объекта между учетными записями хранения;

Можно асинхронно копировать BLOB-объекты между учетными записями хранения и областями или внутри них.

Следующий пример демонстрирует копирование BLOB-объектов из одной учетной записи хранения в другую. Сначала мы создадим контейнер в исходной учетной записи хранения, задав общий доступ для чтения к его большим двоичным объектам. Далее мы передадим файл в контейнер и, наконец, скопируем большой двоичный объект из этого контейнера в контейнер в целевой учетной записи хранения.

В этом примере для успешного выполнения операции копирования целевой контейнер уже должен существовать в целевой учетной записи хранения. Кроме того, исходный большой двоичный объект, указанный в аргументе `--source-uri`, либо должен включать в себя токен подписанного URL-адреса (SAS), либо быть общедоступным, как в данном примере.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта

Удалите большой двоичный объект из контейнера с помощью команды [az storage blob delete](/cli/azure/storage/blob#az_storage_blob_delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>Определение типа содержимого

Тип содержимого (или тип MIME) определяет формат данных в большом двоичном объекте. Браузеры и другое программное обеспечение используют тип содержимого, чтобы определять способы обработки данных. В следующем примере задается тип содержимого `image/png`.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>отображение и изменение свойств большого двоичного объекта и метаданных;

Каждый большой двоичный объект имеет несколько определяемых службой свойств (в том числе имя, тип, длину и другие свойства), которые можно отобразить с помощью команды [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show). Вы можете также настроить большой двоичный объект, задав для него свойства и их значения по своему усмотрению. Для этого используйте команду [az storage blob metadata update](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_update).

В этом примере мы сначала отобразим определяемые службой свойства большого двоичного объекта, а затем обновим его, добавив несколько свойств метаданных по своему усмотрению. В итоге с помощью команды [az storage blob metadata show](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_show) мы получим свойства метаданных большого двоичного объекта и их значения.

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Создание подписанного URL-адреса (SAS)

Подписанный URL-адрес (SAS) позволяет вам предоставлять другим клиентам ограниченный доступ к объектам в вашей учетной записи хранения без ключей доступа к этой учетной записи. Чтобы получить универсальный код ресурса (URI), который предоставляет доступ к закрытому ресурсу, необходимо создать маркер SAS с необходимыми разрешениями и окном срока действия (в течение которого маркер является действительным). Его необходимо добавить как строку запроса в URL-адрес ресурса, получив, таким образом, полный универсальный код ресурса (URI) маркера SAS. Любой пользователь, у которого есть URL-адрес ресурса и маркер SAS может получить доступ к этому закрытому ресурсу в течение срока действия маркера SAS. Например, вы можете разрешить доступ на чтение к закрытому большому двоичному объекту в течение двух минут, и тогда его смогут просмотреть.

Далее вы отключите общий доступ к контейнеру, выполните тестирование закрытого доступа, а затем создадите и проверите универсальный код ресурса SAS.

### <a name="disable-container-public-access"></a>Отключение общего доступа к контейнеру

Сначала необходимо установить для уровня доступа к контейнеру состояние `off`. Это означает, что к контейнеру или его большим двоичным объектам нет доступа при отсутствии подписанногоо URL-адреса или ключа доступа к учетной записи хранения.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Проверка закрытого доступа

Чтобы проверить, что к контейнеру отсутствует общий доступ на чтение больших двоичных объектов, необходимо получить URL-адрес одного из больших двоичных объектов. Для этого используйте команду [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Щелкните URL-адрес большого двоичного объекта в новом окне браузера в конфиденциальном режиме. Вы получите ошибку `ResourceNotFound`, так как большой двоичный объект является закрытым (а подписанный URL-адрес вы не использовали).

### <a name="create-a-sas-uri"></a>Создание универсального кода ресурса SAS

Теперь мы создадим универсальный код ресурса SAS для получения доступа к большому двоичному объекту. В примере ниже для одной переменной мы используем URL-адрес большого двоичного объекта, который получим с помощью команды [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url), а для другой переменной — маркер SAS, который получим с помощью команды [az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas). В итоге, объединив эти два значения, отделенных с помощью разделителя строки запроса `?`, мы получим полный универсальный код ресурса маркера SAS для большого двоичного объекта.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Проверка универсального кода ресурса маркера SAS

В новом конфиденциальном окне браузера перейдите к полному универсальному коду ресурса SAS, который вы получили с помощью команды `echo` во фрагменте кода выше. В этот раз вы не получите ошибку и сможете просмотреть или загрузить большой двоичный объект.

Дождитесь истечения срока действия URL-адреса (две минуты в этом примере), а затем перейдите на него в другом конфиденциальном окне браузера. Вы получите ошибку `AuthenticationFailed`, так как срок действия маркера SAS истек.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая созданную учетную запись хранения и большие двоичные объекты, загруженные в рамках примеров в этой статье, удалите группу ресурсов с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы ознакомились с основными задачами при работе с большими двоичными объектами в службе хранилища Azure:

> [!div class="checklist"]
> * Создание контейнера
> * отправка файла (большого двоичного объекта) в контейнер;
> * Перечисление BLOB-объектов в контейнере
> * Скачивание большого двоичного объекта из контейнера
> * копирование большого двоичного объекта между учетными записями хранения;
> * Удаление большого двоичного объекта
> * отображение и изменение свойств большого двоичного объекта и метаданных;
> * управление безопасностью с помощью подписанного URL-адреса (SAS).

Дополнительные сведения о работе с Azure CLI, а также работе с ресурсами в учетной записи хранения см. в источниках ниже.

* Инфраструктура CLI Azure
  * [Вход с помощью Azure CLI 2.0](/cli/azure/authenticate-azure-cli). Узнайте о различных методах проверки подлинности с помощью CLI, включая неинтерактивный вход через [субъект-службу](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) для запуска автоматических скриптов Azure CLI.
  * [Azure CLI 2.0 command reference](/cli/azure/) (Справочник по командам Azure CLI 2.0)
* Обозреватель службы хранилища Microsoft Azure
  * [Обозреватель службы хранилища Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) — это бесплатное автономное приложение от Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.

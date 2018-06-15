---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью Azure CLI | Документация Майкрософт
description: В рамках этого краткого руководства вы используете Azure CLI в хранилище объектов (больших двоичных объектов). отправите большой двоичный объект в службу хранилища Azure, скачаете его и составите список больших двоичных объектов в контейнере с помощью Azure CLI.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: b55fe97adc56f4b8f1baeb2d8bb5f905fcd424bd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415696"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure CLI

Azure CLI — это интерфейс командной строки Azure для управления ресурсами Azure. Вы можете использовать его в браузере с Azure Cloud Shell. Его также можно установить в macOS, Linux или Windows и запускать из командной строки. В этом кратком руководстве содержатся сведения об использовании Azure CLI для отправки и скачивания данных в хранилище BLOB-объектов Azure и обратно.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Выполните команду `az --version`, чтобы определить версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Вы можете упорядочивать группы больших двоичных объектов аналогично организации файлов в папках на компьютере.

Создайте контейнер для хранения больших двоичных объектов с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Передача больших двоичных объектов

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Большинство файлов, находящихся в хранилище BLOB-объектов, хранятся как блочные BLOB-объекты. Добавочные BLOB-объекты используются, когда данные необходимо добавить в имеющийся большой двоичный объект без изменения его текущего содержимого, например для ведения журнала. Страничные BLOB-объекты содержат файлы VHD виртуальных машин IaaS.

Сначала создайте файл для передачи большого двоичного объекта.
Если вы используете Azure Cloud Shell, создайте файл, выполнив следующие инструкции: введите `vi helloworld` при открытии файла, нажмите клавишу **INSERT**, введите "Hello world", а затем нажмите клавишу **ESC**, введите `:x` и нажмите клавишу **ВВОД**.

В этом примере большой двоичный объект отправляется в контейнер, созданный на последнем шаге, с помощью команды [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Если вы использовали описанный выше метод,чтобы создать файл в Azure Cloud Shell, вместо этого можно применить следующую команду CLI (обратите внимание, что вам не нужно было указывать путь к файлу, так как файл был создан в базовом каталоге, — в других ситуациях путь потребуется указать):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

С помощью этой операции создается большой двоичный объект, если он не был создан ранее, или же, если он имеется, происходит его замещение. Прежде чем продолжить, отправьте нужное количество файлов.

Чтобы одновременно отправить несколько файлов, используйте команду [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch).

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Выведите список больших двоичных объектов в контейнере, выполнив команду [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Загрузка BLOB-объектов

Выполните команду [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download), чтобы скачать отправленный ранее большой двоичный объект.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Передача данных с помощью AzCopy

Программа [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) является дополнительным способом высокопроизводительной передачи данных с поддержкой сценариев для хранилища Azure. AzCopy можно использовать для передачи данных из хранилища BLOB-объектов, таблиц и файлов и обратно.

В качестве краткого примера здесь приводится команда AzCopy для отправки файла *myfile.txt* в контейнер *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая учетную запись хранения, созданную в рамках этого краткого руководства, удалите группу ресурсов с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать файлы между локальным диском и контейнером в хранилище BLOB-объектов Azure. Дополнительные сведения о работе с большими двоичными объектами в службе хранилища Azure см. в руководстве по работе с хранилищем BLOB-объектов Azure.

> [!div class="nextstepaction"]
> [Perform Blob storage operations with Azure CLI](storage-how-to-use-blobs-cli.md) (Выполнение операций в хранилище BLOB-объектов с помощью Azure CLI)

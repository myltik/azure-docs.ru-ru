---
title: Перенаправление событий хранилища BLOB-объектов Azure в пользовательскую конечную веб-точку | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для подписки на события хранилища BLOB-объектов.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: f0764ebc423cfb5323f2b634ce5a5ecbe075135c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-azure-cli"></a>Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку с помощью Azure CLI

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье с помощью интерфейса командной строки Azure вы создадите подписку на события хранилища BLOB-объектов и активируете событие, чтобы увидеть результат. 

Как правило, события можно отправлять в конечную точку, реагирующую на событие, такую как веб-перехватчик или функция Azure. Для упрощения примера, показанного в этой статье, мы будем отправлять события на URL-адрес, предназначенный только для сбора сообщений. Этот URL-адрес можно создать с помощью средства стороннего производителя [Hookbin](https://hookbin.com/).

> [!NOTE]
> Средство **Hookbin** не предназначено для использования с высокой пропускной способностью. Это средство используется исключительно для демонстрации. Если вы одновременно отправляете несколько событий, в средстве могут отобразиться не все события.

Выполнив действия, описанные в этой статье, вы увидите, что данные событий отправлены в конечную точку.

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.24 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Темами событий сетки являются ресурсы Azure, которые необходимо поместить в группу ресурсов Azure. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). 

В следующем примере создается группа ресурсов с именем `<resource_group_name>` в расположении *westcentralus*.  Замените `<resource_group_name>` уникальным именем для группы ресурсов.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Для использования событий хранилища BLOB-объектов требуется либо [учетная запись хранения BLOB-объектов](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts), либо [учетная запись хранения общего назначения версии 2](../common/storage-account-options.md#general-purpose-v2). Учетные записи **общего назначения версии 2 (GPv2)** являются учетными записями хранения, которые поддерживают все функции для всех служб хранилища, включая большие двоичные объекты, файлы, очереди и таблицы. **Учетные записи хранения BLOB-объектов** — это специализированные учетные записи хранения таких неструктурированных данных, как большие двоичные объекты, в службе хранилища Azure. Учетные записи хранилища BLOB-объектов похожи на учетные записи хранения общего назначения и обладают такими же функциями обеспечения устойчивости, надежности, масштабируемости и производительности, которые вы уже используете, а также отличаются полной согласованностью API в плане блочных BLOB-объектов и добавления больших двоичных объектов. Для приложений, требующих только блокировки или добавления больших двоичных объектов, рекомендуется использовать учетные записи хранения больших двоичных объектов. 

> [!NOTE]
> Доступность событий хранилища привязывается к [доступности](../../event-grid/overview.md) службы "Сетка событий". Эта функция скоро станет доступной в других регионах вместе со службой "Сетка событий".

Замените `<storage_account_name>` уникальным именем своей учетной записи хранения, а `<resource_group_name>` — именем группы ресурсов, созданной ранее.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Создание конечной точки сообщения

Перед подпиской на раздел необходимо создать конечную точку для сообщения о событии. Прежде чем записывать код для реагирования на событие, давайте создадим конечную точку, которая собирает сообщения, чтобы можно было их просматривать. Hookbin — это средство стороннего производителя, которое позволяет создать конечную точку и просматривать отправленные в нее запросы. Перейдите на сайт [Hookbin](https://hookbin.com/) и нажмите кнопку **Create New Endpoint** (Создать конечную точку).  Скопируйте URL-адрес ячейки, так как он понадобится при подписке на тему.

## <a name="subscribe-to-your-storage-account"></a>Подписка на учетную запись хранения

Подпишитесь на тему, чтобы определить в сетке событий Azure, какие события вам необходимо отслеживать. В следующем примере создается подписка на созданную учетную запись хранения и передается URL-адрес из Hookbin в качестве конечной точки для уведомления о событии. Замените `<event_subscription_name>` уникальным именем для вашей подписки на события, а `<endpoint_URL>` — значением из предыдущего раздела. Указав конечную точку при подписке, служба "Сетка событий Azure" обрабатывает маршрутизацию событий для этой конечной точки. Для `<resource_group_name>` и `<storage_account_name>` используйте созданные ранее значения.  

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="trigger-an-event-from-blob-storage"></a>Активация события из хранилища BLOB-объектов

Теперь необходимо активировать событие, чтобы увидеть, как Сетка событий Azure распределяет сообщение к вашей конечной точке. Сначала мы настроим имя и ключ учетной записи хранения, затем создадим контейнер, после чего создадим и отправим файл. Еще раз: для `<storage_account_name>` и `<resource_group_name>` используются значения, созданные ранее.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Перейдите по URL-адресу, созданному ранее. Или нажмите кнопку "Обновить" в открытом браузере. Вы видите отправленные события. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжать работу с этой учетной записью хранения и подпиской на события, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

Замените `<resource_group_name>` именем группы ресурсов, созданной ранее.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как создавать разделы и подписки на события, ознакомьтесь с дополнительными сведениями о событиях хранилища BLOB-объектов и возможностях службы "Сетка событий":

- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов)
- [An introduction to Azure Event Grid](../../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")

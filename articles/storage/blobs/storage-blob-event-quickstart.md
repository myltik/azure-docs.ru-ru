---
title: "Перенаправление событий хранилища BLOB-объектов Azure в пользовательскую конечную веб-точку (предварительная версия) | Документация Microsoft"
description: "Используйте службу \"Сетка событий Azure\" для подписки на события хранилища BLOB-объектов."
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: 67f262913333fb69f5b862fa3d862c0d773e4172
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2017
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье с помощью интерфейса командной строки Azure вы создадите подписку на события хранилища BLOB-объектов и активируете событие, чтобы увидеть результат. 

Как правило, события можно отправлять в конечную точку, реагирующую на событие, такую как веб-перехватчик или функция Azure. Для упрощения примера, показанного в этой статье, мы будем отправлять события на URL-адрес, предназначенный только для сбора сообщений. Создать этот URL-адрес можно с помощью стороннего инструмента с открытым кодом [RequestBin](https://requestb.in/).

> [!NOTE]
> **RequestBin** — средство с открытым исходным кодом, не предназначенное для использования с высокой пропускной способностью. Это средство используется здесь исключительно в целях демонстрации. Если вы одновременно отправляете несколько событий, в средстве могут отобразиться не все события.

Выполнив действия, описанные в этой статье, вы увидите, что данные событий отправлены в конечную точку.

![Данные событий](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.14 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Темами событий сетки являются ресурсы Azure, которые необходимо поместить в группу ресурсов Azure. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). 

В следующем примере создается группа ресурсов с именем `<resource_group_name>` в расположении *westcentralus*.  Замените `<resource_group_name>` уникальным именем для группы ресурсов.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Создание учетной записи хранения больших двоичных объектов

Для использования службы хранилища Azure вам потребуется учетная запись хранения.  События хранилища BLOB-объектов сейчас доступны только в учетных записях хранения больших двоичных объектов.

Учетные записи хранения BLOB-объектов — это специализированные учетные записи хранения таких неструктурированных данных, как большие двоичные объекты, в службе хранилища Azure. Учетные записи хранения BLOB-объектов аналогичны учетным записям хранения общего назначения и обладают такими же функциями обеспечения устойчивости, надежности, масштабируемости и производительности, которые вы уже используете, а также отличаются полной согласованностью API в плане блочных BLOB-объектов и добавления больших двоичных объектов. Для приложений, требующих только блокировки или добавления больших двоичных объектов, рекомендуется использовать учетные записи хранения больших двоичных объектов.

> [!NOTE]
> Сейчас служба "Сетка событий" находится на этапе предварительной версии и доступна только для учетных записей хранения в регионах **westcentralus** и **westus2**.

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

Перед подпиской на события хранилища BLOB-объектов необходимо создать конечную точку для сообщения о событии. Прежде чем писать код для реагирования на событие, мы создадим конечную точку, которая собирает сообщения, чтобы можно было их просматривать. RequestBin является сторонним средством с открытым кодом, позволяющим создать конечную точку и просматривать запросы, отправленные на нее. Последовательно выберите пункты [RequestBin](https://requestb.in/)и щелкните **Создать RequestBin**.  Скопируйте URL-адрес ячейки, так как он понадобится при подписке на тему.

## <a name="subscribe-to-your-blob-storage-account"></a>Подписка на учетную запись хранения BLOB-объектов

Подпишитесь на тему, чтобы определить в сетке событий Azure, какие события вам необходимо отслеживать. В следующем примере создается подписка на созданную учетную запись хранения BLOB-объектов и передается URL-адрес из RequestBin в качестве конечной точки для уведомления о событии. Замените `<event_subscription_name>` уникальным именем для вашей подписки на события, а `<URL_from_RequestBin>` — значением из предыдущего раздела. Указав конечную точку при подписке, служба "Сетка событий Azure" обрабатывает маршрутизацию событий для этой конечной точки. Для `<resource_group_name>` и `<storage_account_name>` используйте созданные ранее значения. 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
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

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Просмотрите URL-адрес RequestBin, созданный ранее. Или нажмите кнопку "Обновить" в вашем открытом браузере RequestBin. Вы видите отправленные события. 

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
  }
}]

```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжать работу с этой учетной записью хранения и подпиской на события, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

Замените `<resource_group_name>` именем группы ресурсов, созданной ранее.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знаете, как создавать разделы и подписки на события, ознакомьтесь с дополнительными сведениями о событиях хранилища BLOB-объектов и возможностях службы "Сетка событий":

- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов)
- [An introduction to Azure Event Grid](../../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")

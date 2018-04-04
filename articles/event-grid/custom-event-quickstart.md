---
title: Пользовательские события для службы "Сетка событий Azure" с интерфейсом командной строки | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" и Azure CLI, чтобы иметь возможность публиковать темы и подписываться на эти события.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: be98fa88727a867e0fca0ca3587db276f8a2d7f2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Создание и перенаправление пользовательских событий с помощью Azure CLI и службы "Сетка событий"

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье используется интерфейс командной строки Azure, чтобы создать пользовательскую тему, подписаться на тему и активировать событие, чтобы увидеть результат. Как правило, можно отправлять события в конечную точку, реагирующую на событие, например, веб-перехватчик или функция Azure. Однако для упрощения этой статьи можно отправлять события по URL-адресу, где собраны все сообщения. Этот URL-адрес можно создать с помощью стороннего средства от [Hookbin](https://hookbin.com/).

>[!NOTE]
>Средство **Hookbin** не предназначено для использования с высокой пропускной способностью. Это средство используется исключительно для демонстрации. Если вы одновременно отправляете несколько событий, в средстве могут отобразиться не все события.

По завершении можно увидеть, что данные событий отправлены в конечную точку.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.24 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Темами событий сетки являются ресурсы Azure, которые необходимо поместить в группу ресурсов Azure. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). 

В следующем примере создается группа ресурсов с именем *gridResourceGroup* в расположении *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Создание пользовательской темы

Раздел сетки событий содержит определяемую пользователем конечную точку, в которой можно размещать свои события. В следующем примере создается пользовательский раздел в вашей группе ресурсов. Замените `<topic_name>` уникальным именем для вашей темы. Имя раздела должно быть уникальным, так как оно представлено записью службы доменных имен (DNS).

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Создание конечной точки сообщения

Перед подпиской на раздел необходимо создать конечную точку для сообщения о событии. Прежде чем записывать код для реагирования на событие, давайте создадим конечную точку, которая собирает сообщения, чтобы можно было их просматривать. Hookbin — это стороннее средство, которое позволяет создать конечную точку и просматривать запросы, отправленные на нее. Перейдите на сайт [Hookbin](https://hookbin.com/) и нажмите кнопку **Create New Endpoint** (Создать конечную точку).  Скопируйте URL-адрес ячейки, так как он понадобится при создании подписки на пользовательский раздел.

## <a name="subscribe-to-a-topic"></a>Оформление подписки на тему

Подпишитесь на тему, чтобы определить в сетке событий Azure, какие события вам необходимо отслеживать. В следующем примере создается подписка на созданный раздел и передается URL-адрес из Hookbin в качестве конечной точки для уведомления о событии. Замените `<event_subscription_name>` уникальным именем для вашей подписки, а `<endpoint_URL>` — значением из предыдущей темы. Указав конечную точку при подписке, служба "Сетка событий Azure" обрабатывает маршрутизацию событий для этой конечной точки. Для `<topic_name>` используйте созданное ранее значение. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Отправка события в тему

Давайте активируем событие, чтобы увидеть, как служба "Сетка событий" отправляет сообщение в конечную точку. Сначала получите URL-адрес и ключ для пользовательского раздела. Еще раз используйте имя раздела для `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Для простоты используйте пример данных события для отправки в раздел. Как правило, приложение или служба Azure отправит данные события. Ниже приведен пример получения данных события:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Чтобы просмотреть все сведения о событии, используйте `echo "$body"`. Элемент `data` JSON отображает полезные данные события. Любое значение JSON с правильным форматом может быть в этом поле. Кроме того, можно использовать поле темы для дополнительной маршрутизации и фильтрации.

CURL — это служебная программа, которая отправляет HTTP-запросы. В этой статье мы используем CURL для отправки события в раздел. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Перейдите по URL-адресу, созданному ранее. Или нажмите кнопку "Обновить" в открытом браузере. Вы видите отправленные события. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжить работу с этим событием, не удаляйте ресурсы, созданные при работе с этой статьей. В противном случае удалите соответствующие ресурсы с помощью следующей команды.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как создавать темы и подписки на события, ознакомьтесь с дополнительными сведениями о сетке событий, которые могут помочь вам:

- [An introduction to Azure Event Grid](overview.md) (Общие сведения о службе "Сетка событий Azure")
- [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Отслеживание изменений виртуальной машины с помощью Azure Logic Apps и службы "Сетка событий Azure")
- [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md)

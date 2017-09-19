---
title: "Пользовательские события для службы \"Сетка событий Azure\" | Документы Microsoft"
description: "Используйте службу \"Сетка событий Azure\", чтобы иметь возможность публиковать темы и подписываться на эти события."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 08/15/2017
ms.topic: hero-article
ms.service: event-grid
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: cd285471196f75f6a8c8ead0e2895fd71414f223
ms.contentlocale: ru-ru
ms.lasthandoff: 09/13/2017

---

# <a name="create-and-route-custom-events-with-azure-event-grid"></a>Создание и перенаправление пользовательского события со службой "Сетка событий Azure"

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье используется интерфейс командной строки Azure, чтобы создать пользовательскую тему, подписаться на тему и активировать событие, чтобы увидеть результат. Как правило, можно отправлять события в конечную точку, реагирующую на событие, например, веб-перехватчик или функция Azure. Однако для упрощения этой статьи можно отправлять события по URL-адресу, где собраны все сообщения. Создать этот URL-адрес можно с помощью стороннего инструмента с открытым кодом [RequestBin](https://requestb.in/).

>[!NOTE]
>**RequestBin** — средство с открытым исходным кодом, не предназначенное для использования с высокой пропускной способностью. Это средство используется здесь исключительно в целях демонстрации. Если вы одновременно отправляете несколько событий, в средстве могут отобразиться не все события.

После завершения можно увидеть, что данные события сохранены в конечную точку.

![Данные событий](./media/custom-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.14 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Темами событий сетки являются ресурсы Azure, которые необходимо поместить в группу ресурсов Azure. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). 

В следующем примере создается группа ресурсов с именем *gridResourceGroup* в расположении *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Создание пользовательской темы

Тема содержит определяемую пользователем конечную точку, в которой можно размещать свои события. В следующем примере создается тема в вашей группе ресурсов. Замените `<topic_name>` уникальным именем для вашей темы. Имя раздела должно быть уникальным, так как оно представлено записью службы доменных имен (DNS). Предварительная версия службы "Сетка событий Azure" поддерживает расположения **westus2** и **westcentralus**.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Создание конечной точки сообщения

Перед подпиской на раздел необходимо создать конечную точку для сообщения о событии. Прежде чем записывать код для реагирования на событие, давайте создадим конечную точку, которая собирает сообщения, чтобы можно было их просматривать. RequestBin является сторонним средством с открытым кодом, позволяющим создать конечную точку и просматривать запросы, отправленные на нее. Последовательно выберите пункты [RequestBin](https://requestb.in/)и щелкните **Создать RequestBin**.  Скопируйте URL-адрес ячейки, так как он понадобится при подписке на тему.

## <a name="subscribe-to-a-topic"></a>Оформление подписки на тему

Подпишитесь на тему, чтобы определить в сетке событий Azure, какие события вам необходимо отслеживать. В следующем примере выполняется подписка на созданный раздел и передается URL-адрес из RequestBin в качестве конечной точки для уведомления о событии. Замените `<event_subscription_name>` уникальным именем для вашей подписки, а `<URL_from_RequestBin>` — значением из предыдущей темы. Указав конечную точку при подписке, служба "Сетка событий Azure" обрабатывает маршрутизацию событий для этой конечной точки. Для `<topic_name>` используйте созданное ранее значение. 

```azurecli-interactive
az eventgrid topic event-subscription create --name <event_subscription_name> \
  --endpoint <URL_from_RequestBin> \
  -g gridResourceGroup \
  --topic-name <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Отправка события в тему

Теперь необходимо активировать событие, чтобы увидеть, как Сетка событий Azure распределяет сообщение к вашей конечной точке. Сначала получите URL-адрес и ключ для темы. Еще раз используйте имя раздела для `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Чтобы упростить эту тему, мы настроили образец данных событий для отправки в раздел. Как правило, приложение или служба Azure отправит данные события. Ниже приведен пример получения данных события:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Если вы используете параметр `echo "$body"`, отобразится полное событие. Элемент `data` JSON отображает полезные данные события. Любое значение JSON с правильным форматом может быть в этом поле. Кроме того, можно использовать поле темы для дополнительной маршрутизации и фильтрации.

CURL — это служебная программа, выполняющая HTTP-запросы. В этой статье мы используем CURL для отправки события в нашу тему. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Просмотрите URL-адрес RequestBin, созданный ранее. Или нажмите кнопку "Обновить" в вашем открытом браузере RequestBin. Вы видите отправленные события. 

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжить работу с этим событием, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знаете, как создавать темы и подписки на события, ознакомьтесь с дополнительными сведениями о сетке событий, которые могут помочь вам:

- [An introduction to Azure Event Grid](overview.md) (Общие сведения о службе "Сетка событий Azure")
- [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Отслеживание изменений виртуальной машины с помощью Azure Logic Apps и службы "Сетка событий Azure")
- [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md)


---
title: Сопоставление настраиваемого поля со схемой службы "Сетка событий Azure"
description: В этой статье описано преобразование настраиваемой схемы в схему службы "Сетка событий Azure".
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301973"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Сопоставление настраиваемых полей со схемой службы "Сетка событий Azure"

Если данные события не соответствуют ожидаемой [схеме службы "Сетка событий"](event-schema.md), вы по-прежнему можете использовать службу "Сетка событий", чтобы выполнять маршрутизацию событий для подписчиков. В этой статье описывается сопоставление схемы со схемой службы "Сетка событий".

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Исходная схема событий

Предположим, что у вас есть приложение, которое отправляет события в следующем формате:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Несмотря на то, что данный формат не соответствует требуемой схеме, служба "Сетка событий" позволяет сопоставлять поля со схемой. Или же вы можете получить значения в исходной схеме.

## <a name="create-custom-topic-with-mapped-fields"></a>Создание настраиваемого раздела с сопоставленными полями

При создании настраиваемого раздела укажите способ сопоставления полей исходного события со схемой службы "Сетка событий". Существует три свойства, которые позволяют настраивать сопоставление.

* Параметр `--input-schema` указывает тип схемы. Доступные варианты: *cloudeventv01schema*, *customeventschema* и *eventgridschema*. Значение по умолчанию — eventgridschema. При создании настраиваемого сопоставления между своей схемой и схемой службы "Сетка событий" используйте значение customeventschema. Если события находятся в схеме CloudEvents, используйте значение cloudeventv01schema.

* Параметр `--input-mapping-default-values` задает значения по умолчанию для полей в схеме службы "Сетка событий". Вы можете задать значения по умолчанию для полей *Subject*, *EventType* и *DataVersion*. Как правило, этот параметр используется, если настраиваемая схема не содержит поле, которое соответствует одному из этих трех полей. Например, можно указать, что поле DataVersion всегда имеет значение **1.0**.

* Параметр `--input-mapping-fields` сопоставляет поля из вашей схемы со схемой службы "Сетка событий". Необходимо указать значения в разделенных пробелами парах "ключ — значение". В качестве имени ключа используйте имя поля сетки событий. В качестве значения используйте имя поля. Вы можете указать имена ключа для полей *Id*, *Topic*, *EventTime*, *Subject*, *EventType* и *DataVersion*.

В следующем примере создается настраиваемый раздел с несколькими сопоставленными полями и полями по умолчанию.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Подписка на раздел службы "Сетка событий"

При подписке на настраиваемый раздел укажите схему, которую вы хотите использовать для получения событий. Используйте параметр `--event-delivery-schema`, задав значение *cloudeventv01schema*, *eventgridschema* или *inputeventschema*. Значение по умолчанию — eventgridschema.

В примерах в этом разделе для обработчика событий используется хранилище очередей. Дополнительные сведения см. в статье [Route custom events to Azure Queue storage with Azure CLI and Event Grid](custom-event-to-queue-storage.md) (Маршрутизация настраиваемых событий в хранилище очередей Azure с помощью Azure CLI и службы "Сетки событий").

В следующем примере показана подписка на раздел службы "Сетка событий" и использование схемы службы "Сетка событий" по умолчанию.

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

В следующем примере используется входная схема события:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Публикация события в разделе

Теперь вы можете отправить событие в настраиваемый раздел и просмотреть результат сопоставления. Отправьте событие в [пример схемы](#original-event-schema) с помощью следующего скрипта:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Теперь просмотрите хранилище очередей. Две подписки доставили события в разных схемах.

Первая подписка использует схему сетки событий. Событие доставлено в следующем формате:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Эти поля содержат сопоставления из настраиваемого раздела. Поле **myEventTypeField** сопоставляется с полем **EventType**. Для полей **DataVersion** и **Subject** используются значения по умолчанию. Объект **Data** содержит поля исходной схемы событий.

Вторая подписка использует входную схему событий. Событие доставлено в следующем формате:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Обратите внимание, что были доставлены исходные поля.

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).

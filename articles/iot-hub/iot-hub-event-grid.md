---
title: Центр Интернета вещей Azure и служба "Сетка событий" | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для активации процессов на основе действий, выполняемых в Центре Интернета вещей.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: f187aa81ca519f2597657f01c2d7a630740b5348
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634317"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Реагирование на события в Центре Интернета вещей, используя службу "Сетка событий" для запуска действий (предварительная версия)

Центр Интернета вещей Azure интегрируется со службой "Сетка событий Azure", чтобы вы могли отправлять оповещения о событиях в другие службы и активировать нисходящие процессы. Настройте свои бизнес-приложения для ожидания передачи данных событий Центра Интернета вещей, чтобы реагировать на критические события более надежным, масштабируемым и безопасным способом. Например, можно создать приложение для выполнения нескольких действий, таких как обновление базы данных, создание запроса и доставка уведомления по электронной почте, каждый раз, когда в концентраторе Центра Интернета вещей регистрируется новое устройство. 

[Сетка событий Azure][lnk-eg-overview] — это полностью управляемая служба маршрутизации событий, использующая принцип "публикации — подписки". Эта служба поддерживает такие службы Azure, как [Функции Azure](../azure-functions/functions-overview.md) и [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), и может доставлять оповещения о событиях в службы за пределами Azure с помощью веб-перехватчиков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid][lnk-eg-overview] (Общие сведения о службе "Сетка событий Azure"). 

![Архитектура службы "Сетка событий Azure"](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Доступ по регионам

Интеграция со службой "Сетка событий" доступна для центров IoT, расположенных в регионах, где поддерживается эта служба. Наиболее актуальный список регионов см. в статье [Общие сведения о службе "Сетка событий Azure"][lnk-eg-overview]. 

## <a name="event-types"></a>Типы событий

Центр Интернета вещей публикует следующие типы событий: 

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Публикуется при регистрации устройства в Центре Интернета вещей. |
| Microsoft.Devices.DeviceDeleted | Публикуется при удалении устройства из Центра Интернета вещей. | 

Чтобы настроить события, которые будут публиковаться в каждом Центре Интернета вещей, используйте портал Azure или Azure CLI. Например, ознакомьтесь со статьей [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps). 

## <a name="event-schema"></a>Схема событий

События Центра Интернета вещей содержат все сведения, необходимые для реагирования на изменения в жизненном цикле устройства. Вы можете идентифицировать событие Центра Интернета вещей, проверив, запускается ли свойство eventType с **Microsoft.Devices**. Дополнительные сведения об использовании свойств событий службы "Сетка событий" см. в статье [Схема событий службы "Сетка событий Azure"](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Схема, созданная устройством

В следующем примере показана схема события создания устройства: 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Дополнительные описания каждого свойства см. в статье [Azure Event Grid event schema for IoT Hub](../event-grid/event-schema-iot-hub.md) (Схема событий службы "Сетка событий Azure" для Центра Интернета вещей).

## <a name="filter-events"></a>События фильтра

Подписки событий Центра Интернета вещей могут фильтровать события по типу событий и имени устройства. Фильтры тем в службе "Сетка событий Azure" работают на основе соответствия **префиксов** и **суффиксов**. В фильтре используется оператор `AND`, чтобы события с темой, которая соответствует и префиксу, и суффиксу, доставлялись подписчику. 

Тема событий Интернета вещей использует следующий формат:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Советы по потреблению событий

Для работы с приложениями, обрабатывающими события Центра Интернета вещей, нужно следовать таким рекомендациям:

* Вы можете настроить несколько подписок для маршрутизации событий в один обработчик событий. Не следует предполагать, что события поступают из определенного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что оно поступает из ожидаемого Центра Интернета вещей. 
* Не следует предполагать, что все получаемые события имеют ожидаемые типы. Всегда проверяйте eventType перед обработкой сообщения.
* Сообщения могут прибывать не по порядку или с задержкой. Используйте поле ETag, чтобы понять, является ли информация об объектах актуальной.



## <a name="next-steps"></a>Дополнительная информация

* [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps)
* [An introduction to Azure Event Grid][lnk-eg-overview] (Общие сведения о службе "Сетка событий Azure")
* [Сравнение маршрутизации сообщений со службой "Сетка событий" и без нее для Центра Интернета вещей][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md
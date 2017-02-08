---
title: "Настраиваемые схемы отслеживания | Документация Майкрософт"
description: "Дополнительные сведения о настраиваемых схемах отслеживания"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ef7681f1411e1ea5b3411b714d5fb9f5a8be4f17


---
# <a name="custom-tracking-schemas"></a>Настраиваемые схемы отслеживания
Настраиваемую схему отслеживания можно использовать в учетной записи интеграции Azure для мониторинга транзакций типа "бизнес-бизнес" (B2B).

## <a name="custom-tracking-schema"></a>Настраиваемая схема отслеживания
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Свойство | Тип | Описание |
| --- | --- | --- |
| sourceType |   | Тип источника выполнения. Допустимые значения — **Microsoft.Logic/workflows** или **custom**. (обязательный параметр) |
| Источник |   | Если тип источника — **Microsoft.Logic/workflows**, то сведения источника должны следовать этой схеме. Если тип источника — **custom**, то используется схема JToken. (обязательный параметр) |
| systemId | Строка | Системный идентификатор приложения логики. (обязательный параметр) |
| runId | Строка | Идентификатор выполнения приложения логики. (обязательный параметр) |
| operationName | Строка | Имя операции (например, действие или триггер). (обязательный параметр) |
| repeatItemScopeName | string | Повторяет имя элемента, если действие находится внутри цикла `foreach`/ или `until`. (обязательный параметр) |
| repeatItemIndex | Целое число  | Определяет, находится ли действие внутри цикла `foreach`/ или `until`. Указывает индекс повторяющегося элемента. (обязательный параметр) |
| trackingId | Строка | Идентификатор отслеживания для корреляции сообщений. (необязательный параметр) |
| correlationId | string | Идентификатор корреляции для корреляции сообщений. (необязательный параметр) |
| clientRequestId | Строка | Клиент может включить его для корреляции сообщений. (необязательный параметр) |
| eventLevel |   | Уровень события. (обязательный параметр) |
| eventTime |   | Время события в формате UTC (ГГГГ-ММ-ДДTЧЧ:ММ:СС.00000Z). (обязательный параметр) |
| recordType |   | Тип записи отслеживания. Допустимое значение — **custom**. (обязательный параметр) |
| record |   | Настраиваемый тип записи. Разрешен формат JToken. (обязательный параметр) |

## <a name="b2b-protocol-tracking-schemas"></a>Схемы отслеживания для протоколов B2B
Сведения о схемах отслеживания для протоколов B2B см. в следующих статьях:
* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Схемы отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md).   
* Дополнительные сведения об [отслеживании сообщений B2B на портале Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Дополнительные сведения о [Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO3-->



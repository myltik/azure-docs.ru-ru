---
title: "Настраиваемая схема отслеживания | Документация Майкрософт"
description: "Дополнительные сведения о настраиваемой схеме отслеживания"
author: padmavc
manager: erikre
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>Настраиваемая схема отслеживания
## <a name="supported-custom-tracking-schema"></a>Поддерживаемая настраиваемая схема отслеживания
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

| Свойство | Описание |
| --- | --- |
| sourceType |(Обязательный параметр.)  Указывает тип источника выполнения. Допустимые значения — Microsoft.Logic/workflows или custom. |
| Источник |Обязательное. Если тип источника — Microsoft.Logic/workflows, то сведения источника должны следовать этой схеме. Если тип источника — custom, используется схема JToken.  |
| systemId |Обязательное, строка.  Указывает идентификатор системы приложения логики. |
| runId |Обязательное, строка.  Указывает идентификатор выполнения приложения логики. |
| operationName |Обязательное, строка.  Указывает имя операции (например, действие или триггер). |
| repeatItemScopeName |Обязательное, строка. Указывает имя повторяемого элемента, если действие входит в цикл foreach или Until. |
| repeatItemIndex |Обязательное, целое число.  Если действие входит в цикл foreach или Until, свойство указывает индекс повторяющегося элемента.  |
| trackingId |Необязательное, строка. Указывает идентификатор отслеживания для корреляции сообщений. |
| correlationId |Необязательное, строка. Указывает идентификатор корреляции для корреляции сообщений. |
| clientRequestId |Необязательное, строка.  Клиент может заполнить его для корреляции сообщений. |
| eventLevel |(Обязательный параметр.) Указывает уровень события. |
| eventTime |(Обязательный параметр.) Указывает время события в формате UTC (ГГГГ-ММ-ДДTЧЧ:ММ:СС.00000Z). |
| recordType |Обязательное. Указывает тип записи отслеживания. Допустимое значение — Custom. |
| record |(Обязательный параметр.)  Указывает тип пользовательской записи. Допустимый формат — JToken. |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>Поддерживаемые схемы отслеживания для протоколов B2B
* [Схема отслеживания AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Схема отслеживания X12](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>Дальнейшие действия
[Мониторинг сообщений B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Отслеживание сообщений B2B на портале OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->



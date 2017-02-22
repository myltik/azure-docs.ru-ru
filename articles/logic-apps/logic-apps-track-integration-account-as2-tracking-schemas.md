---
title: "Схемы отслеживания AS2 для мониторинга сообщений B2B в Azure Logic Apps | Документация Майкрософт"
description: "Используйте схемы отслеживания AS2 для мониторинга сообщений из транзакций типа &quot;бизнес — бизнес&quot; (B2B) в учетной записи интеграции Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: b78c42b14005b9bcedbc29216541495fd19c64a5


---
# <a name="as2-tracking-schemas"></a>Схемы отслеживания AS2
Схемы отслеживания AS2 можно использовать в учетной записи интеграции Azure для мониторинга транзакций типа "бизнес — бизнес" (B2B):

* Схема отслеживания сообщений AS2
* Схема отслеживания уведомлений о состоянии сообщений AS2

## <a name="as2-message-tracking-schema"></a>Схема отслеживания сообщений AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Свойство | Тип | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | string | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | Строка | Имя получателя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя отправителя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | string | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | Строка | Идентификатор сообщения AS2 из заголовков сообщения AS2 (необязательный параметр) |
| dispositionType |Строка | Значение типа метода обработки уведомлений о состоянии сообщения (MDN). (необязательный параметр) |
| fileName | string | Имя файла из заголовка сообщения AS2. (необязательный параметр) |
| isMessageFailed |Логический | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned | Логический | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isMessageEncrypted | Логический | Указывает, зашифровано ли сообщение AS2. (обязательный параметр) |
| isMessageCompressed |Логический | Указывает, сжато ли сообщение AS2. (обязательный параметр) |
| correlationMessageId | Строка | Идентификатор сообщения AS2, используемый для корреляции сообщений с уведомлениями о состоянии сообщений. (необязательный параметр) |
| incomingHeaders |Словарь JToken | Сведения о заголовке входящего сообщения AS2. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Сведения о заголовке исходящего сообщения AS2. (необязательный параметр) |
| isNrrEnabled | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| isMdnExpected | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| mdnType | Перечисление. | Допустимые значения: **NotConfigured**, **Sync** и **Async**. (обязательный параметр) |

## <a name="as2-mdn-tracking-schema"></a>Схема отслеживания уведомлений о состоянии сообщений AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Свойство | Тип | Описание |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | string | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | string | Имя партнера-получателя сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя партнера-отправителя сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction |string | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | string | Идентификатор сообщения AS2. (необязательный параметр) |
| originalMessageId |string | Исходный идентификатор сообщения AS2. (необязательный параметр) |
| dispositionType | Строка | Значение типа метода обработки уведомлений о состоянии сообщения. (необязательный параметр) |
| isMessageFailed |Логический | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned |Логический | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isNrrEnabled | Логический | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| statusCode | Перечисление. | Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| micVerificationStatus | Перечисление. | Допустимые значения: **NotApplicable**, **Succeeded** и **Failed**. (обязательный параметр) |
| correlationMessageId | string | Идентификатор корреляции. Исходный идентификатор сообщения (идентификатор сообщения, для которого настроена отправка уведомлений о состоянии сообщения). (необязательный параметр) |
| incomingHeaders | Словарь JToken | Указывает сведения о заголовке входящего сообщения. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Указывает сведения о заголовке исходящего сообщения. (необязательный параметр) |

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md).   
* Дополнительные сведения о [настраиваемых схемах отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Дополнительные сведения о [схемах отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Дополнительные сведения об [отслеживании сообщений B2B на портале Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).



<!--HONumber=Jan17_HO4-->



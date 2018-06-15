---
title: Схемы отслеживания AS2 для мониторинга сообщений B2B в Azure Logic Apps | Документация Майкрософт
description: Используйте схемы отслеживания AS2 для мониторинга сообщений из транзакций типа "бизнес — бизнес" (B2B) в учетной записи интеграции Azure.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300887"
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Запуск или включение отслеживания сообщений AS2 и уведомлений MDN для мониторинга успешно доставленных сообщений, ошибок и свойств сообщений
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

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | Строка | Имя получателя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя отправителя сообщения AS2 из заголовков сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Строка | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | Строка | Идентификатор сообщения AS2 из заголовков сообщения AS2 (необязательный параметр) |
| dispositionType |Строка | Значение типа метода обработки уведомлений о состоянии сообщения (MDN). (необязательный параметр) |
| fileName | Строка | Имя файла из заголовка сообщения AS2. (необязательный параметр) |
| isMessageFailed |Логическое | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned | Логическое | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isMessageEncrypted | Логическое | Указывает, зашифровано ли сообщение AS2. (обязательный параметр) |
| isMessageCompressed |Логическое | Указывает, сжато ли сообщение AS2. (обязательный параметр) |
| correlationMessageId | Строка | Идентификатор сообщения AS2, используемый для корреляции сообщений с уведомлениями о состоянии сообщений. (необязательный параметр) |
| incomingHeaders |Словарь JToken | Сведения о заголовке входящего сообщения AS2. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Сведения о заголовке исходящего сообщения AS2. (необязательный параметр) |
| isNrrEnabled | Логическое | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
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

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения AS2. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения AS2. (необязательный параметр) |
| as2To | Строка | Имя партнера-получателя сообщения AS2. (обязательный параметр) |
| as2From | Строка | Имя партнера-отправителя сообщения AS2. (обязательный параметр) |
| agreementName | Строка | Имя соглашения AS2, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction |Строка | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| messageId | Строка | Идентификатор сообщения AS2. (необязательный параметр) |
| originalMessageId |Строка | Исходный идентификатор сообщения AS2. (необязательный параметр) |
| dispositionType | Строка | Значение типа метода обработки уведомлений о состоянии сообщения. (необязательный параметр) |
| isMessageFailed |Логическое | Указывает, произошел ли сбой сообщения AS2. (обязательный параметр) |
| isMessageSigned |Логическое | Указывает, подписано ли сообщение AS2. (обязательный параметр) |
| isNrrEnabled | Логическое | Если значение неизвестно, то используйте значение по умолчанию. (обязательный параметр) |
| statusCode | Перечисление. | Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| micVerificationStatus | Перечисление. | Допустимые значения: **NotApplicable**, **Succeeded** и **Failed**. (обязательный параметр) |
| correlationMessageId | Строка | Идентификатор корреляции. Исходный идентификатор сообщения (идентификатор сообщения, для которого настроена отправка уведомлений о состоянии сообщения). (необязательный параметр) |
| incomingHeaders | Словарь JToken | Указывает сведения о заголовке входящего сообщения. (необязательный параметр) |
| outgoingHeaders |Словарь JToken | Указывает сведения о заголовке исходящего сообщения. (необязательный параметр) |

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о [Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md).   
* Дополнительные сведения о [настраиваемых схемах отслеживания B2B](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Дополнительные сведения о [схемах отслеживания X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Дополнительные сведения об [отслеживании сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

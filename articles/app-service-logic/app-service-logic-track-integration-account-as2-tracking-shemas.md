---
title: "Схема отслеживания AS2 | Документация Майкрософт"
description: "Сведения о схеме отслеживания AS2"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>Схемы отслеживания AS2
Поддерживаемые схемы отслеживания AS2:

* схема отслеживания сообщений AS2;
* схема отслеживания уведомлений о состоянии сообщений AS2.

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

| Свойство | Описание |
| --- | --- |
| senderPartnerName |Необязательное, строка.  Указывает имя партнера отправителя сообщения AS2. |
| receiverPartnerName |Необязательное, строка.  Указывает имя партнера получателя сообщения AS2. |
| as2To |Обязательное, строка.  Указывает имя получателя сообщения AS2, включенное в заголовки сообщения AS2. |
| as2From |Обязательное, строка. Указывает имя отправителя сообщения AS2, включенное в заголовки сообщения AS2. |
| agreementName |Необязательное, строка.  Имя соглашения AS2, в соответствии с которым разрешаются сообщения. |
| direction |Обязательное, строка.  Указывает направление потока обработки сообщений (отправленных или полученных). |
| messageId |Необязательное, строка.  Указывает идентификатор сообщения AS2, включенный в заголовки сообщения AS2. |
| dispositionType |Необязательное, строка. Указывает значение типа метода обработки уведомления о состоянии сообщения. |
| fileName |Необязательное, строка.  Указывает имя файла, включенного в заголовок сообщения AS2. |
| isMessageFailed |Обязательное, логическое значение.  Указывает, удалось ли отправить сообщение AS2. |
| isMessageSigned |Обязательное, логическое значение.  Указывает, подписано ли сообщение AS2. |
| isMessageEncrypted |Обязательное, логическое значение. Указывает, зашифровано ли сообщение AS2. |
| isMessageCompressed |Обязательное, логическое значение. Указывает, сжато ли сообщение AS2. |
| correlationMessageId |Необязательное, строка. Указывает идентификатор сообщения AS2, используемый для корреляции сообщений с уведомлениями о состоянии сообщений. |
| incomingHeaders |Необязательное, словарь JToken.  Указывает сведения о заголовке входящего сообщения AS2. |
| outgoingHeaders |Необязательное, словарь JToken.  Указывает сведения о заголовке исходящего сообщения AS2. |
| isNrrEnabled |Обязательное, логическое значение.  Если значение неизвестно, используйте значение по умолчанию. |
| isMdnExpected |Обязательное, логическое значение. Если значение неизвестно, используйте значение по умолчанию. |
| mdnType |Обязательное, перечисление. Допустимые значения: NotConfigured или Sync (Async). |

## <a name="as2-mdn-tracking-schema"></a>Схема отслеживания уведомлений о состоянии сообщения AS2
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

| Свойство | Описание |
| --- | --- |
| senderPartnerName |Необязательное, строка. Указывает имя партнера отправителя сообщения AS2. |
| receiverPartnerName |Необязательное, строка. Указывает имя партнера получателя сообщения AS2. |
| as2To |Обязательное, строка. Указывает имя партнера-получателя сообщения AS2. |
| as2From |Обязательное, строка. Указывает имя партнера-отправителя сообщения AS2. |
| agreementName |Необязательное, строка. Имя соглашения AS2, в соответствии с которым разрешаются сообщения. |
| direction |Обязательное, строка. Указывает направление потока обработки сообщений (отправленных или полученных). |
| messageId |Необязательное, строка. Указывает идентификатор сообщения AS2. |
| originalMessageId |Необязательное, строка. Указывает исходный идентификатор сообщения AS2. |
| dispositionType |Необязательное, строка. Указывает значение типа метода обработки уведомления о состоянии сообщения. |
| isMessageFailed |Обязательное, логическое значение. Указывает, удалось ли отправить сообщение AS2. |
| isMessageSigned |Обязательное, логическое значение. Указывает, подписано ли сообщение AS2. |
| isNrrEnabled |Обязательное, логическое значение.  Если значение неизвестно, используйте значение по умолчанию. |
| statusCode |Обязательное, перечисление.  Допустимые значения: Accepted (Rejected) или AcceptedWithErrros. |
| micVerificationStatus |Обязательное, перечисление.  Допустимые значения: NotApplicable или Succeeded (Failed). |
| correlationMessageId |Необязательное, строка.  Указывает идентификатор корреляции.  Идентификатор корреляции — это исходный идентификатор сообщения (идентификатор сообщения, для которого настроена отправка уведомления о состоянии сообщения). |
| incomingHeaders |Необязательное, словарь JToken.  Указывает сведения о заголовке входящего сообщения. |
| outgoingHeaders |Необязательное, словарь JToken.  Указывает сведения о заголовке исходящего сообщения. |

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[Мониторинг сообщений B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Отслеживание сообщений B2B на портале OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Настраиваемая схема отслеживания](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Схемы отслеживания X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->



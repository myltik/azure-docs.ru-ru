---
title: Схемы отслеживания X12 для мониторинга сообщений B2B в Azure Logic Apps | Документация Майкрософт
description: Используйте схемы отслеживания X12 для мониторинга сообщений из транзакций типа "бизнес — бизнес" (B2B) в учетной записи интеграции Azure.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 385ff73d780f62f19af2face591cd0b2291ef396
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300979"
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Запуск или включение отслеживания сообщений X12 для мониторинга успешно доставленных сообщений, ошибок и свойств сообщений
Схемы отслеживания X12 можно использовать в учетной записи интеграции Azure для мониторинга транзакций типа "бизнес-бизнес" (B2B):

* схема отслеживания набора транзакций X12;
* схема отслеживания подтверждения набора транзакций X12;
* схема отслеживания обмена X12;
* схема отслеживания подтверждения обмена X12;
* схема отслеживания функциональной группы X12;
* схема отслеживания подтверждения для функциональной группы X12.

## <a name="x12-transaction-set-tracking-schema"></a>Схема отслеживания набора транзакций X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена. (необязательный параметр) |
| functionalGroupControlNumber | Строка | Функциональный контрольный номер. (необязательный параметр) |
| transactionSetControlNumber | Строка | Контрольный номер набора транзакций. (необязательный параметр) |
| CorrelationMessageId | Строка | Идентификатор сообщения корреляции. Сочетание свойств {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (необязательный параметр) |
| messageType | Строка | Набор транзакций или тип документа. (необязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| isTechnicalAcknowledgmentExpected | Логическое | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isFunctionalAcknowledgmentExpected | Логическое | Указывает, настроено ли в соглашении X12 функциональное подтверждение. (обязательный параметр) |
| needAk2LoopForValidMessages | Логическое | Указывает, требуется ли цикл AK2 для допустимого сообщения. (обязательный параметр) |
| segmentsCount | Целое число  | Число сегментов в наборе транзакций X12. (необязательный параметр) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Схема отслеживания подтверждения набора транзакций X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена для функционального подтверждения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (необязательный параметр) |
| functionalGroupControlNumber | Строка | Контрольный номер функциональной группы для функционального подтверждения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (необязательный параметр) |
| isaSegment | Строка | Сегмент ISA сообщения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (необязательный параметр) |
| gsSegment | Строка | Сегмент GS сообщения. Значение заполняется только для отправляющей стороны при получении функционального подтверждения для сообщений, отправленных партнеру. (необязательный параметр) |
| respondingfunctionalGroupControlNumber | Строка | Контрольный номер обмена в ответе. (необязательный параметр) |
| respondingFunctionalGroupId | Строка | Идентификатор функциональной группы в ответе, сопоставляющийся с AK101 в подтверждении. (необязательный параметр) |
| respondingtransactionSetControlNumber | Строка | Контрольный номер набора транзакций в ответе. (необязательный параметр) |
| respondingTransactionSetId | Строка | Идентификатор набора транзакций в ответе, сопоставляющийся с AK201 в подтверждении. (необязательный параметр) |
| statusCode | Логическое | Код состояния подтверждения для набора транзакций. (обязательный параметр) |
| segmentsCount | Перечисление. | Код состояния подтверждения. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Перечисление. | Состояние обработки подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| CorrelationMessageId | Строка | Идентификатор сообщения корреляции. Сочетание свойств {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (необязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| ak2Segment | Строка | Подтверждение для набора транзакций в пределах полученной функциональной группы. (необязательный параметр) |
| ak3Segment | Строка | Сообщает об ошибках в сегменте данных. (необязательный параметр) |
| ak5Segment | Строка | Сообщает о том, принят ли определенный в сегменте AK2 набор транзакций, а также сообщает соответствующую причину. (необязательный параметр) |

## <a name="x12-interchange-tracking-schema"></a>Схема отслеживания обмена X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена. (необязательный параметр) |
| isaSegment | Строка | Сегмент ISA сообщения. (необязательный параметр) |
| isTechnicalAcknowledgmentExpected | Логическое | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| isa09 | Строка | Дата обмена документа X12. (необязательный параметр) |
| isa10 | Строка | Время обмена документа X12. (необязательный параметр) |
| isa11 | Строка | Идентификатор стандартов управления обмена X12. (необязательный параметр) |
| isa12 | Строка | Контрольный номер версии обмена X12. (необязательный параметр) |
| isa14 | Строка | Запрос подтверждения X12. (необязательный параметр) |
| isa15 | Строка | Индикатор для тестирования или работы. (необязательный параметр) |
| isa16 | Строка | Разделитель элементов. (необязательный параметр) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Схема отслеживания подтверждения обмена X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена для технического подтверждения, полученного от партнеров. (необязательный параметр) |
| isaSegment | Строка | Сегмент ISA для технического подтверждения, полученного от партнеров. (необязательный параметр) |
| respondingInterchangeControlNumber |Строка | Контрольный номер обмена для технического подтверждения, полученного от партнеров. (необязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| statusCode | Перечисление. | Код состояния подтверждения обмена. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Перечисление. | Состояние подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| ta102 | Строка | Дата обмена. (необязательный параметр) |
| ta103 | Строка | Время обмена. (необязательный параметр) |
| ta105 | Строка | Код примечания обмена. (необязательный параметр) |

## <a name="x12-functional-group-tracking-schema"></a>Схема отслеживания функциональной группы X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена. (необязательный параметр) |
| functionalGroupControlNumber | Строка | Функциональный контрольный номер. (необязательный параметр) |
| gsSegment | Строка | Сегмент GS сообщения. (необязательный параметр) |
| isTechnicalAcknowledgmentExpected | Логическое | Указывает, настроено ли в соглашении X12 техническое подтверждение. (обязательный параметр) |
| isFunctionalAcknowledgmentExpected | Логическое | Указывает, настроено ли в соглашении X12 функциональное подтверждение. (обязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр)|
| gs01 | Строка | Код функционального идентификатора. (необязательный параметр) |
| gs02 | Строка | Код отправителя приложения. (необязательный параметр) |
| gs03 | Строка | Код получателя приложения. (необязательный параметр) |
| gs04 | Строка | Дата функциональной группы. (необязательный параметр) |
| gs05 | Строка | Время функциональной группы. (необязательный параметр) |
| gs07 | Строка | Код ответственного агентства. (необязательный параметр) |
| gs08 | Строка | Код версии, выпуска и промышленного идентификатора. (необязательный параметр) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>схема отслеживания подтверждения для функциональной группы X12.
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| senderPartnerName | Строка | Имя партнера отправителя сообщения X12. (необязательный параметр) |
| receiverPartnerName | Строка | Имя партнера получателя сообщения X12. (необязательный параметр) |
| senderQualifier | Строка | Отправка квалификатора партнера. (обязательный параметр) |
| senderIdentifier | Строка | Отправка идентификатора партнера. (обязательный параметр) |
| receiverQualifier | Строка | Получение квалификатора партнера. (обязательный параметр) |
| receiverIdentifier | Строка | Получение идентификатора партнера. (обязательный параметр) |
| agreementName | Строка | Имя соглашения X12, в соответствии с которым разрешаются сообщения. (необязательный параметр) |
| direction | Перечисление. | Направление потока получаемых или отправляемых сообщений. (обязательный параметр) |
| interchangeControlNumber | Строка | Контрольный номер обмена, который заполняется для отправляющей стороны при получении технического подтверждения от партнеров. (необязательный параметр) |
| functionalGroupControlNumber | Строка | Контрольный номер функциональной группы технического подтверждения, который заполняется для отправляющей стороны при получении технического подтверждения от партнеров. (необязательный параметр) |
| isaSegment | Строка | Как и контрольный номер обмена, но заполняется только в определенных случаях. (необязательный параметр) |
| gsSegment | Строка | Как и контрольный номер функциональной группы, но заполняется только в определенных случаях. (необязательный параметр) |
| respondingfunctionalGroupControlNumber | Строка | Контрольный номер исходной функциональной группы. (необязательный параметр) |
| respondingFunctionalGroupId | Строка | Сопоставляется с AK101 в идентификаторе функциональной группы подтверждения. (необязательный параметр) |
| isMessageFailed | Логическое | Указывает, произошел ли сбой сообщения X12. (обязательный параметр) |
| statusCode | Перечисление. | Код состояния подтверждения. Допустимые значения: **Accepted**, **Rejected** и **AcceptedWithErrros**. (обязательный параметр) |
| processingStatus | Перечисление. | Состояние обработки подтверждения. Допустимые значения: **Received**, **Generated** и **Sent**. (обязательный параметр) |
| ak903 | Строка | Число полученных наборов транзакций. (необязательный параметр) |
| ak904 | Строка | Число наборов транзакций, принятых в указанной функциональной группе. (необязательный параметр) |
| ak9Segment | Строка | Указывает, принята ли определенная в сегменте AK1 функциональная группа, а также сообщает соответствующую причину. (необязательный параметр) |

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о [мониторинге сообщений B2B](logic-apps-monitor-b2b-message.md).
* Дополнительные сведения о [схемах отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Дополнительные сведения о [настраиваемых схемах отслеживания B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Дополнительные сведения об [отслеживании сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Дополнительные сведения о [Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md).  

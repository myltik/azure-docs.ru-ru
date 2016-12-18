---
title: "Добавление соединителя Twilio в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Twilio с параметрами API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Начало работы с соединителем Twilio
Подключитесь к Twilio для отправки и получения глобальных SMS-, MMS- и IP-сообщений.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

С помощью Twilio вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Twilio; 
* использовать действия для получения сообщений, вывода списка сообщений и выполнения многих других задач. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, новое полученное сообщение Twilio можно использовать в рабочем процессе служебной шины. 

Сведения о добавлении операции в приложения логики см. в статье [Создание нового приложения логики, подключающего службы SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель Twilio позволяет выполнять следующие действия. Триггеры отсутствуют. 

| триггеры; | Действия |
| --- | --- |
| None |<ul><li>Получение сообщения</li><li>Вывод списка сообщений</li><li>Отправка сообщения</li></ul> |

Все соединители поддерживают данные в форматах JSON и XML. 

## <a name="create-a-connection-to-twilio"></a>Создание подключения к Twilio
При добавлении этого соединителя в приложения логики введите указанные ниже значения Twilio:

| Свойство | Обязательно | Описание |
| --- | --- | --- |
| Идентификатор учетной записи |Да |Введите идентификатор своей учетной записи Twilio |
| Маркер доступа |Да |Введите свой маркер доступа Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Если у вас нет маркера доступа, прочитайте в статье про [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) о том, как его создать.

> [!TIP]
> Это подключение к Twilio можно использовать в других приложениях логики.
> 
> 

## <a name="swagger-rest-api-reference"></a>Справочник по REST API Swagger
#### <a name="this-documentation-is-for-version-10"></a>Эта документация предназначена для версии 1.0
### <a name="get-message"></a>Получение сообщения
Возвращает одно сообщение, определяемое предоставленным идентификатором сообщения.  
```GET: /Messages/{MessageId}.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| MessageId |строка |Да |path |None |Идентификатор сообщения |

### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 404 |Сообщение не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="list-messages"></a>Вывод списка сообщений
Возвращает список сообщений, связанных с вашей учетной записью.  
```GET: /Messages.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| Кому |строка |Нет |запрос |None |На номер телефона |
| Из |строка |Нет |запрос |None |С номера телефона |
| DateSent |строка |Нет |запрос |None |Отображение только сообщений, отправленных в указанную дату (в формате GMT), заданную в виде ГГГГ-ММ-ДД. Пример: DateSent = 2009-07-06. Можно также указать неравенство, например DateSent < = ГГГГ-ММ-ДД для сообщений, отправленных в полночь или до полуночи указанной даты, и DateSent > = ГГГГ-ММ-ДД для сообщений, отправленных в полночь или после полуночи указанной даты. |
| PageSize |целое число |Нет |запрос |50 |Количество ресурсов, возвращаемых на каждой странице списка. Значение по умолчанию — 50. |
| Страница |целое число |Нет |запрос |0 |Номер страницы. Значение по умолчанию — 0. |

### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="send-message"></a>Отправка сообщения
Отправляет новое сообщение на номер мобильного телефона.  
```POST: /Messages.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Да |текст |None |Отправляемое сообщение |

### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: модель запроса для операции отправки сообщения
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Из |строка |Да |
| Кому |строка |Да |
| текст |строка |Да |
| media_url |array |Нет |
| status_callback |string |Нет |
| messaging_service_sid |string |Нет |
| application_sid |string |Нет |
| max_price |string |Нет |

#### <a name="message-model-for-message"></a>Message: модель для сообщения
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| текст |строка |Нет |
| Из |строка |Нет |
| Кому |строка |Нет |
| status |строка |Нет |
| sid |строка |Нет |
| account_sid |string |Нет |
| api_version |string |Нет |
| num_segments |string |Нет |
| num_media |string |Нет |
| date_created |string |Нет |
| date_sent |string |Нет |
| date_updated |string |Нет |
| direction |строка |Нет |
| error_code |string |Нет |
| error_message |string |Нет |
| price |строка |Нет |
| price_unit |string |Нет |
| uri |строка |Нет |
| subresource_uris |array |Нет |
| messaging_service_sid |string |Нет |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: модель ответа для операции вывода списка сообщений
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| messages |array |Нет |
| Страница |целое число |Нет |
| page_size |целое число |Нет |
| num_pages |целое число |Нет |
| uri |строка |Нет |
| first_page_uri |string |Нет |
| next_page_uri |string |Нет |
| total |целое число |Нет |
| previous_page_uri |string |Нет |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: модель ответа для операции вывода списка сообщений
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| incoming_phone_numbers |array |Нет |
| Страница |целое число |Нет |
| page_size |целое число |Нет |
| num_pages |целое число |Нет |
| uri |строка |Нет |
| first_page_uri |string |Нет |
| next_page_uri |string |Нет |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: модель запроса для операции добавления входящего номера
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| PhoneNumber |строка |Да |
| AreaCode |строка |Нет |
| FriendlyName |строка |Нет |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: входящий телефонный номер
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| phone_number |string |Нет |
| friendly_name |string |Нет |
| sid |строка |Нет |
| account_sid |string |Нет |
| date_created |string |Нет |
| date_updated |string |Нет |
| capabilities |не определено |Нет |
| status_callback |string |Нет |
| status_callback_method |string |Нет |
| api_version |string |Нет |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities: возможности номеров телефонов
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| mms |Логическое |Нет |
| sms |Логическое |Нет |
| voice |Логическое |Нет |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: доступные номера телефонов
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| phone_number |string |Нет |
| friendly_name |string |Нет |
| lata |строка |Нет |
| latitude |строка |Нет |
| longitude |строка |Нет |
| postal_code |string |Нет |
| rate_center |string |Нет |
| region |строка |Нет |
| mms |Логическое |Нет |
| sms |Логическое |Нет |
| voice |Логическое |Нет |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: класс записей об использовании
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| категория |строка |Нет |
| usage |строка |Нет |
| usage_unit |string |Нет |
| Описание |строка |Нет |
| price |number |Нет |
| price_unit |string |Нет |
| count |строка |Нет |
| count_unit |string |Нет |
| start_date |string |Нет |
| end_date |string |Нет |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->



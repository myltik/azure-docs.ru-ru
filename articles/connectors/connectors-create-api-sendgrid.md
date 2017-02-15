---
title: "SendGrid | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad35f6590a39972ec9be7168059738b6daae0574


---
# <a name="get-started-with-the-sendgrid-connector"></a>Начало работы с соединителем SendGrid
Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview. 
> 
> 

Для начала можно создать приложение логики, как описано [здесь](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель SendGrid можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML. 

 Соединитель SendGrid поддерживает описанные ниже действия. Триггеры отсутствуют.

### <a name="sendgrid-actions"></a>Действия SendGrid
Вы можете выполнять перечисленные ниже действия:

| Действие | Описание |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Отправляет сообщение электронной почты с помощью интерфейса API SendGrid (максимум 10 000 получателей) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Добавление отдельного получателя в список получателей |

## <a name="create-a-connection-to-sendgrid"></a>Создание подключения к SendGrid
Для создания приложений логики с помощью SendGrid необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| ApiKey |Да |Укажите ключ API SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> Это подключение можно использовать в других приложениях логики.
> 
> 

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров, описанных в этой статье.

## <a name="reference-for-sendgrid"></a>Справочник по SendGrid
Относится к версии 1.0.

## <a name="sendemail"></a>SendEmail
Отправка электронной почты: отправляет сообщение электронной почты с помощью интерфейса API SendGrid (максимум 10 000 получателей) 

```POST: /api/mail.send.json``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| запрос | |Да |текст |Нет |Отправляемое сообщение |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 429 |Слишком много запросов |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="addrecipienttolist"></a>AddRecipientToList
Добавление получателя в список: добавление отдельного получателя в список получателей 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| listId |string |Да |path |Нет |Уникальный идентификатор списка получателей |
| recipientId |string |Да |path |Нет |Уникальный идентификатор получателя |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
### <a name="emailrequest"></a>EmailRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Из |string |Да |
| fromname |string |Нет |
| значение |string |Да |
| toname |string |Нет |
| subject |string |Да |
| текст |string |Да |
| ishtml |Логическое |Нет |
| cc |string |Нет |
| ccname |string |Нет |
| bcc |string |Нет |
| bccname |string |Нет |
| replyto |string |Нет |
| дата |string |Нет |
| headers |string |Нет |
| файлов |array |Нет |
| filenames |array |Нет |

### <a name="emailresponse"></a>EmailResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| message |string |Нет |

### <a name="recipientlists"></a>RecipientLists
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| lists |array |Нет |

### <a name="recipientlist"></a>RecipientList
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |целое число |Нет |
| name |string |Нет |
| recipient_count |целое число |Нет |

### <a name="recipients"></a>Recipients
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| recipients |array |Нет |

### <a name="recipient"></a>Recipient
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| email |string |Нет |
| last_name |string |Нет |
| first_name |string |Нет |
| id |string |Нет |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->



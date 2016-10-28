<properties
pageTitle="MailChimp | Microsoft Azure"
description="Создание приложений логики с помощью службы приложений Azure. MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Начало работы с соединителем MailChimp

MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их.


>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.

Для начала можно создать приложение логики, как указано в соответствующей [статье](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Триггеры и действия

Соединитель MailChimp можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML.

 Соединитель MailChimp предоставляет следующие триггеры и действия:

### Действия MailChimp
Вы можете выполнять перечисленные ниже действия:

|Действие|Описание|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Создание кампании на основе типа кампании, списка получателей и параметров кампании (тема, название, отправитель, получатель)|
|[newlist](connectors-create-api-mailchimp.md#newlist)|Создание списка в учетной записи MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Добавление или изменение элемента списка|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Удаление элемента из списка|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Изменение сведений для определенного элемента списка|
### Триггеры MailChimp
Можно прослушивать указанные ниже события:

|Триггер | Описание|
|--- | ---|
|При добавлении элемента в список|Запускает рабочий процесс при добавлении нового элемента в список|
|При создании списка|Запускает рабочий процесс при создании списка|


## Создание подключения к MailChimp
Для создания приложений логики с помощью MailChimp необходимо создать **подключение**, а затем указать данные для следующих свойств.

|Свойство| Обязательно|Описание|
| ---|---|---|
|Маркер|Да|Укажите учетные данные MailChimp|

>[AZURE.INCLUDE [Шаги по созданию подключения к MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Это подключение можно использовать в других приложениях логики.

## Справочник по MailChimp
Относится к версии 1.0.

## newcampaign
Новая кампания: создание кампании на основе типа кампании, списка получателей и параметров кампании (тема, название, отправитель, получатель)

```POST: /campaigns```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|newCampaignRequest| |Да|текст|Нет|Объект JSON, отправляемый в тексте с параметрами запроса новой кампании|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## newlist
Новый список: создание списка в учетной записи MailChimp

```POST: /lists```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|newListRequest| |Да|текст|Нет|Объект JSON, отправляемый в тексте с параметрами запроса новой кампании|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## addmember
Добавление элемента в список: добавление или изменение элемента списка

```POST: /lists/{list_id}/members```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|list\_id|string|Да|path|Нет|Уникальный идентификатор для списка|
|newMemberInList| |Да|текст|Нет|Объект JSON, отправляемый в тексте со сведениями о новом элементе|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## removemember
Удаление элемента из списка: удаление элемента из списка

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|list\_id|string|Да|path|Нет|Уникальный идентификатор для списка|
|member\_email|string|Да|path|Нет|Адрес электронной почты удаляемого элемента|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## updatemember
Изменение сведений об элементе: изменение сведений для определенного элемента списка

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Description (Описание)|
| ---|---|---|---|---|---|
|list\_id|string|Да|path|Нет|Уникальный идентификатор для списка|
|member\_email|string|Да|path|Нет|Уникальный адрес электронной почты изменяемого элемента|
|updateMemberInListRequest| |Да|текст|Нет|Объект JSON, отправляемый в тексте со сведениями об изменяемом элементе|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## OnMemberSubscribed
При добавлении элемента в список: запускает рабочий процесс при добавлении нового элемента в список

```GET: /trigger/lists/{list_id}/members```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|list\_id|string|Да|path|Нет|Уникальный идентификатор для списка|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|202|Принято|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## OnCreateList
При создании списка: запускает рабочий процесс при создании списка

```GET: /trigger/lists```

Для этого вызова параметры отсутствуют
#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|202|Принято|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## Определения объектов 

### NewCampaignRequest


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|type|string|Да |
|recipients|не определено|Да |
|Параметры|не определено|Да |
|variate\_settings|не определено|Нет |
|tracking|не определено|Нет |
|rss\_opts|не определено|Нет |
|social\_card|не определено|Нет |



### Recipient


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|list\_id|string|Да |
|segment\_opts|не определено|Нет |



### Параметры


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|subject\_line|string|Да |
|title|string|Нет |
|from\_name|string|Да |
|reply\_to|string|Да |
|use\_conversation|Логическое|Нет |
|to\_name|string|Нет |
|folder\_id|целое число|Нет |
|authenticate|Логическое|Нет |
|auto\_footer|Логическое|Нет |
|inline\_css|Логическое|Нет |
|auto\_tweet|Логическое|Нет |
|auto\_fb\_post|array|Нет |
|fb\_comments|Логическое|Нет |



### Variate\_Settings


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|winner\_criteria|string|Нет |
|wait\_time|целое число|Нет |
|test\_size|целое число|Нет |
|subject\_lines|array|Нет |
|send\_times|array|Нет |
|from\_names|array|Нет |
|reply\_to\_addresses|array|Нет |



### Отслеживание


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|opens|Логическое|Нет |
|html\_clicks|Логическое|Нет |
|text\_clicks|Логическое|Нет |
|goal\_tracking|Логическое|Нет |
|ecomm360|Логическое|Нет |
|google\_analytics|string|Нет |
|clicktale|string|Нет |
|salesforce|не определено|Нет |
|highrise|не определено|Нет |
|capsule|не определено|Нет |



### RSS\_Opts


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|feed\_url|string|Нет |
|frequency|string|Нет |
|constrain\_rss\_img|string|Нет |
|schedule|не определено|Нет |



### Social\_Card


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|image\_url|string|Нет |
|description|string|Нет |
|title|string|Нет |



### Segment\_Opts


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|saved\_segment\_id|целое число|Нет |
|match|string|Нет |



### Salesforce


| Имя свойства | Тип данных | Обязательное |
|---|---|---|
|campaign|Логическое|Нет |
|HDInsight|Логическое|Нет |



### Highrise


| Имя свойства | Тип данных | Обязательное |
|---|---|---|
|campaign|Логическое|Нет |
|HDInsight|Логическое|Нет |



### Capsule


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|HDInsight|Логическое|Нет |



### Расписание


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|hour|целое число|Нет |
|daily\_send|не определено|Нет |
|weekly\_send\_day|string|Нет |
|monthly\_send\_date|number|Нет |



### Daily\_Send


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|sunday|Логическое|Нет |
|monday|Логическое|Нет |
|tuesday|Логическое|Нет |
|wednesday|Логическое|Нет |
|thursday|Логическое|Нет |
|friday|Логическое|Нет |
|saturday|Логическое|Нет |



### CampaignResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|id|string|Нет |
|type|string|Нет |
|create\_time|string|Нет |
|archive\_url|string|Нет |
|status|string|Нет |
|emails\_sent|целое число|Нет |
|send\_time|string|Нет |
|content\_type|string|Нет |
|recipient|array|Нет |
|Параметры|не определено|Нет |
|variate\_settings|не определено|Нет |
|tracking|не определено|Нет |
|rss\_opts|не определено|Нет |
|ab\_split\_opts|не определено|Нет |
|social\_card|не определено|Нет |
|report\_summary|не определено|Нет |
|delivery\_status|не определено|Нет |
|\_links|array|Нет |



### AB\_Split\_Opts


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|split\_test|string|Нет |
|pick\_winner|string|Нет |
|wait\_units|string|Нет |
|wait\_time|целое число|Нет |
|split\_size|целое число|Нет |
|from\_name\_a|string|Нет |
|from\_name\_b|string|Нет |
|reply\_email\_a|string|Нет |
|reply\_email\_b|string|Нет |
|subject\_a|string|Нет |
|subject\_b|string|Нет |
|send\_time\_a|string|Нет |
|send\_time\_b|string|Нет |
|send\_time\_winner|string|Нет |



### Report\_Summary


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|opens|целое число|Нет |
|unique\_opens|целое число|Нет |
|open\_rate|number|Нет |
|clicks|целое число|Нет |
|subscriber\_clicks|number|Нет |
|click\_rate|number|Нет |



### Delivery\_Status


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|включено|Логическое|Нет |
|can\_cancel|Логическое|Нет |
|status|string|Нет |
|emails\_sent|целое число|Нет |
|emails\_canceled|целое число|Нет |



### Ссылка


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|rel|string|Нет |
|href|string|Нет |
|метод|string|Нет |
|targetSchema|string|Нет |
|schema|string|Нет |



### NewListRequest


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|name|string|Да |
|contact|не определено|Да |
|permission\_reminder|string|Да |
|use\_archive\_bar|Логическое|Нет |
|campaign\_defaults|не определено|Да |
|notify\_on\_subscribe|string|Нет |
|notify\_on\_unsubscribe|string|Нет |
|email\_type\_option|Логическое|Да |
|visibility|string|Нет |



### Контакт


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|company|string|Да |
|address1|string|Да |
|address2|string|Нет |
|city|string|Да |
|state|string|Да |
|zip|string|Да |
|country|string|Да |
|phone|string|Да |



### Campaign\_Defaults


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|from\_name|string|Да |
|from\_email|string|Да |
|subject|string|Нет |
|язык|string|Да |



### CreateNewListResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|id|string|Да |
|name|string|Да |
|contact|не определено|Да |
|permission\_reminder|string|Да |
|use\_archive\_bar|Логическое|Нет |
|campaign\_defaults|не определено|Да |
|notify\_on\_subscribe|string|Нет |
|notify\_on\_unsubscribe|string|Нет |
|date\_created|string|Нет |
|list\_rating|целое число|Нет |
|email\_type\_option|Логическое|Да |
|subscribe\_url\_short|string|Нет |
|subscribe\_url\_long|string|Нет |
|beamer\_address|string|Нет |
|visibility|string|Нет |
|modules|array|Нет |
|stats|не определено|Нет |
|\_links|array|Нет |



### Статистика


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|member\_count|целое число|Нет |
|unsubscribe\_count|целое число|Нет |
|cleaned\_count|целое число|Нет |
|member\_count\_since\_send|целое число|Нет |
|unsubscribe\_count\_since\_send|целое число|Нет |
|cleaned\_count\_since\_send|целое число|Нет |
|campaign\_count|целое число|Нет |
|campaign\_last\_sent|целое число|Нет |
|merge\_field\_count|целое число|Нет |
|avg\_sub\_rate|number|Нет |
|avg\_unsub\_rate|number|Нет |
|target\_sub\_rate|number|Нет |
|open\_rate|number|Нет |
|click\_rate|number|Нет |
|last\_sub\_date|string|Нет |
|last\_unsub\_date|string|Нет |



### GetListsResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|lists|array|Нет |
|total\_items|целое число|Нет |



### NewMemberInListRequest


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|email\_type|string|Нет |
|status|string|Да |
|merge\_fields|не определено|Нет |
|interests|string|Нет |
|язык|string|Нет |
|виртуальный IP-адрес|Логическое|Нет |
|location|не определено|Нет |
|email\_address|string|Да |



### FirstAndLastName


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|FNAME|string|Нет |
|LNAME|string|Нет |



### Расположение


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|latitude|number|Нет |
|longitude|number|Нет |



### MemberResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|id|string|Нет |
|email\_address|string|Нет |
|unique\_email\_id|string|Нет |
|email\_type|string|Нет |
|status|string|Нет |
|merge\_fields|не определено|Нет |
|interests|string|Нет |
|stats|не определено|Нет |
|ip\_signup|string|Нет |
|timestamp\_signup|string|Нет |
|ip\_opt|string|Нет |
|timestamp\_opt|string|Нет |
|member\_rating|целое число|Нет |
|last\_changed|string|Нет |
|язык|string|Нет |
|виртуальный IP-адрес|Логическое|Нет |
|email\_client|string|Нет |
|location|не определено|Нет |
|last\_note|не определено|Нет |
|list\_id|string|Нет |
|\_links|array|Нет |



### Last\_Note


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|note\_id|целое число|Нет |
|created\_at|string|Нет |
|created\_by|string|Нет |
|note|string|Нет |



### GetAllMembersResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|members|array|Нет |
|list\_id|string|Нет |
|total\_items|целое число|Нет |



### Объект


| Имя свойства | Тип данных | Обязательно |
|---|---|---|



### UpdateMemberInListRequest


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|email\_address|string|Нет |
|email\_type|string|Нет |
|status|string|Да |
|merge\_fields|не определено|Нет |
|interests|string|Нет |
|язык|string|Нет |
|виртуальный IP-адрес|Логическое|Нет |
|location|не определено|Нет |



### GetMembersResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|members|array|Нет |
|list\_id|string|Нет |
|total\_items|целое число|Нет |



### AddUserResponseModel


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|id|string|Да |
|email\_address|string|Да |
|unique\_email\_id|string|Нет |
|email\_type|string|Нет |
|status|string|Нет |
|merge\_fields|не определено|Да |
|interests|string|Нет |
|stats|не определено|Нет |
|ip\_signup|string|Нет |
|timestamp\_signup|string|Нет |
|ip\_opt|string|Нет |
|timestamp\_opt|string|Нет |
|member\_rating|целое число|Нет |
|last\_changed|string|Нет |
|язык|string|Нет |
|виртуальный IP-адрес|Логическое|Нет |
|email\_client|string|Нет |
|location|не определено|Нет |
|last\_note|не определено|Нет |
|list\_id|string|Нет |
|\_links|array|Нет |


## Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->
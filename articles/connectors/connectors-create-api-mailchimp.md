---
title: "MailChimp | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>Начало работы с соединителем MailChimp
MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель MailChimp можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML.

 Соединитель MailChimp предоставляет следующие триггеры и действия:

### <a name="mailchimp-actions"></a>Действия MailChimp
Вы можете выполнять перечисленные ниже действия:

| Действие | Description (Описание) |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Создание кампании на основе типа кампании, списка получателей и параметров кампании (тема, название, отправитель, получатель) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Создание списка в учетной записи MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Добавление или изменение элемента списка |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Удаление элемента из списка |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Изменение сведений для определенного элемента списка |

### <a name="mailchimp-triggers"></a>Триггеры MailChimp
Можно прослушивать указанные ниже события:

| Триггер | Описание |
| --- | --- |
| При добавлении элемента в список |Запускает рабочий процесс при добавлении нового элемента в список |
| При создании списка |Запускает рабочий процесс при создании списка |

## <a name="create-a-connection-to-mailchimp"></a>Создание подключения к MailChimp
Для создания приложений логики с помощью MailChimp необходимо создать **подключение**, а затем указать данные для следующих свойств.

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Это подключение можно использовать в других приложениях логики.
> 
> 

## <a name="reference-for-mailchimp"></a>Справочник по MailChimp
Относится к версии 1.0.

## <a name="newcampaign"></a>newcampaign
Новая кампания: создание кампании на основе типа кампании, списка получателей и параметров кампании (тема, название, отправитель, получатель)

```POST: /campaigns```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |Да |текст |Нет |Объект JSON, отправляемый в тексте с параметрами запроса новой кампании |

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

## <a name="newlist"></a>newlist
Новый список: создание списка в учетной записи MailChimp

```POST: /lists```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |Да |текст |Нет |Объект JSON, отправляемый в тексте с параметрами запроса новой кампании |

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

## <a name="addmember"></a>addmember
Добавление элемента в список: добавление или изменение элемента списка

```POST: /lists/{list_id}/members```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Да |path |Нет |Уникальный идентификатор для списка |
| newMemberInList | |Да |текст |Нет |Объект JSON, отправляемый в тексте со сведениями о новом элементе |

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

## <a name="removemember"></a>removemember
Удаление элемента из списка: удаление элемента из списка

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Да |path |Нет |Уникальный идентификатор для списка |
| member_email |string |Да |path |Нет |Адрес электронной почты удаляемого элемента |

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

## <a name="updatemember"></a>updatemember
Изменение сведений об элементе: изменение сведений для определенного элемента списка

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Да |path |Нет |Уникальный идентификатор для списка |
| member_email |string |Да |path |Нет |Уникальный адрес электронной почты изменяемого элемента |
| updateMemberInListRequest | |Да |текст |Нет |Объект JSON, отправляемый в тексте со сведениями об изменяемом элементе |

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

## <a name="onmembersubscribed"></a>OnMemberSubscribed
При добавлении элемента в список: запускает рабочий процесс при добавлении нового элемента в список

```GET: /trigger/lists/{list_id}/members```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Да |path |Нет |Уникальный идентификатор для списка |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 202 |Принято |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="oncreatelist"></a>OnCreateList
При создании списка: запускает рабочий процесс при создании списка

```GET: /trigger/lists```

Для этого вызова параметры отсутствуют

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 202 |Принято |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
### <a name="newcampaignrequest"></a>NewCampaignRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| type |string |Да |
| recipients |не определено |Да |
| Параметры |не определено |Да |
| variate_settings |не определено |Нет |
| tracking |не определено |Нет |
| rss_opts |не определено |Нет |
| social_card |не определено |Нет |

### <a name="recipient"></a>Recipient
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| list_id |string |Да |
| segment_opts |не определено |Нет |

### <a name="settings"></a>Параметры
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| subject_line |string |Да |
| title |string |Нет |
| from_name |string |Да |
| reply_to |string |Да |
| use_conversation |Логическое |Нет |
| to_name |string |Нет |
| folder_id |целое число |Нет |
| authenticate |Логическое |Нет |
| auto_footer |Логическое |Нет |
| inline_css |Логическое |Нет |
| auto_tweet |Логическое |Нет |
| auto_fb_post |array |Нет |
| fb_comments |Логическое |Нет |

### <a name="variatesettings"></a>Variate_Settings
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| winner_criteria |string |Нет |
| wait_time |целое число |Нет |
| test_size |целое число |Нет |
| subject_lines |array |Нет |
| send_times |array |Нет |
| from_names |array |Нет |
| reply_to_addresses |array |Нет |

### <a name="tracking"></a>Отслеживание
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| opens |Логическое |Нет |
| html_clicks |Логическое |Нет |
| text_clicks |Логическое |Нет |
| goal_tracking |Логическое |Нет |
| ecomm360 |Логическое |Нет |
| google_analytics |string |Нет |
| clicktale |string |Нет |
| salesforce |не определено |Нет |
| highrise |не определено |Нет |
| capsule |не определено |Нет |

### <a name="rssopts"></a>RSS_Opts
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| feed_url |string |Нет |
| frequency |string |Нет |
| constrain_rss_img |string |Нет |
| schedule |не определено |Нет |

### <a name="socialcard"></a>Social_Card
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| image_url |string |Нет |
| description |string |Нет |
| title |string |Нет |

### <a name="segmentopts"></a>Segment_Opts
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| saved_segment_id |целое число |Нет |
| match |string |Нет |

### <a name="salesforce"></a>Salesforce
| Имя свойства | Тип данных | Обязательное |
| --- | --- | --- |
| campaign |Логическое |Нет |
| HDInsight |Логическое |Нет |

### <a name="highrise"></a>Highrise
| Имя свойства | Тип данных | Обязательное |
| --- | --- | --- |
| campaign |Логическое |Нет |
| HDInsight |Логическое |Нет |

### <a name="capsule"></a>Capsule
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| HDInsight |Логическое |Нет |

### <a name="schedule"></a>Расписание
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| hour |целое число |Нет |
| daily_send |не определено |Нет |
| weekly_send_day |string |Нет |
| monthly_send_date |number |Нет |

### <a name="dailysend"></a>Daily_Send
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| sunday |Логическое |Нет |
| monday |Логическое |Нет |
| tuesday |Логическое |Нет |
| wednesday |Логическое |Нет |
| thursday |Логическое |Нет |
| friday |Логическое |Нет |
| saturday |Логическое |Нет |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |string |Нет |
| type |string |Нет |
| create_time |string |Нет |
| archive_url |string |Нет |
| status |string |Нет |
| emails_sent |целое число |Нет |
| send_time |string |Нет |
| content_type |string |Нет |
| recipient |array |Нет |
| Параметры |не определено |Нет |
| variate_settings |не определено |Нет |
| tracking |не определено |Нет |
| rss_opts |не определено |Нет |
| ab_split_opts |не определено |Нет |
| social_card |не определено |Нет |
| report_summary |не определено |Нет |
| delivery_status |не определено |Нет |
| _links |array |Нет |

### <a name="absplitopts"></a>AB_Split_Opts
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| split_test |string |Нет |
| pick_winner |string |Нет |
| wait_units |string |Нет |
| wait_time |целое число |Нет |
| split_size |целое число |Нет |
| from_name_a |string |Нет |
| from_name_b |string |Нет |
| reply_email_a |string |Нет |
| reply_email_b |string |Нет |
| subject_a |string |Нет |
| subject_b |string |Нет |
| send_time_a |string |Нет |
| send_time_b |string |Нет |
| send_time_winner |string |Нет |

### <a name="reportsummary"></a>Report_Summary
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| opens |целое число |Нет |
| unique_opens |целое число |Нет |
| open_rate |number |Нет |
| clicks |целое число |Нет |
| subscriber_clicks |number |Нет |
| click_rate |number |Нет |

### <a name="deliverystatus"></a>Delivery_Status
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| включено |Логическое |Нет |
| can_cancel |Логическое |Нет |
| status |string |Нет |
| emails_sent |целое число |Нет |
| emails_canceled |целое число |Нет |

### <a name="link"></a>Ссылка
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| rel |string |Нет |
| href |string |Нет |
| метод |string |Нет |
| targetSchema |string |Нет |
| schema |string |Нет |

### <a name="newlistrequest"></a>NewListRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| name |string |Да |
| contact |не определено |Да |
| permission_reminder |string |Да |
| use_archive_bar |Логическое |Нет |
| campaign_defaults |не определено |Да |
| notify_on_subscribe |string |Нет |
| notify_on_unsubscribe |string |Нет |
| email_type_option |Логическое |Да |
| visibility |string |Нет |

### <a name="contact"></a>Контакт
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| company |string |Да |
| address1 |string |Да |
| address2 |string |Нет |
| city |string |Да |
| state |string |Да |
| zip |string |Да |
| country |string |Да |
| phone |string |Да |

### <a name="campaigndefaults"></a>Campaign_Defaults
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| from_name |string |Да |
| from_email |string |Да |
| subject |string |Нет |
| язык |string |Да |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |string |Да |
| name |string |Да |
| contact |не определено |Да |
| permission_reminder |string |Да |
| use_archive_bar |Логическое |Нет |
| campaign_defaults |не определено |Да |
| notify_on_subscribe |string |Нет |
| notify_on_unsubscribe |string |Нет |
| date_created |string |Нет |
| list_rating |целое число |Нет |
| email_type_option |Логическое |Да |
| subscribe_url_short |string |Нет |
| subscribe_url_long |string |Нет |
| beamer_address |string |Нет |
| visibility |string |Нет |
| modules |array |Нет |
| stats |не определено |Нет |
| _links |array |Нет |

### <a name="stats"></a>Статистика
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| member_count |целое число |Нет |
| unsubscribe_count |целое число |Нет |
| cleaned_count |целое число |Нет |
| member_count_since_send |целое число |Нет |
| unsubscribe_count_since_send |целое число |Нет |
| cleaned_count_since_send |целое число |Нет |
| campaign_count |целое число |Нет |
| campaign_last_sent |целое число |Нет |
| merge_field_count |целое число |Нет |
| avg_sub_rate |number |Нет |
| avg_unsub_rate |number |Нет |
| target_sub_rate |number |Нет |
| open_rate |number |Нет |
| click_rate |number |Нет |
| last_sub_date |string |Нет |
| last_unsub_date |string |Нет |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| lists |array |Нет |
| total_items |целое число |Нет |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| email_type |string |Нет |
| status |string |Да |
| merge_fields |не определено |Нет |
| interests |string |Нет |
| язык |string |Нет |
| виртуальный IP-адрес |Логическое |Нет |
| location |не определено |Нет |
| email_address |string |Да |

### <a name="firstandlastname"></a>FirstAndLastName
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| FNAME |string |Нет |
| LNAME |string |Нет |

### <a name="location"></a>Расположение
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| latitude |number |Нет |
| longitude |number |Нет |

### <a name="memberresponsemodel"></a>MemberResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |string |Нет |
| email_address |string |Нет |
| unique_email_id |string |Нет |
| email_type |string |Нет |
| status |string |Нет |
| merge_fields |не определено |Нет |
| interests |string |Нет |
| stats |не определено |Нет |
| ip_signup |string |Нет |
| timestamp_signup |string |Нет |
| ip_opt |string |Нет |
| timestamp_opt |string |Нет |
| member_rating |целое число |Нет |
| last_changed |string |Нет |
| язык |string |Нет |
| виртуальный IP-адрес |Логическое |Нет |
| email_client |string |Нет |
| location |не определено |Нет |
| last_note |не определено |Нет |
| list_id |string |Нет |
| _links |array |Нет |

### <a name="lastnote"></a>Last_Note
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| note_id |целое число |Нет |
| created_at |string |Нет |
| created_by |string |Нет |
| note |string |Нет |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| members |array |Нет |
| list_id |string |Нет |
| total_items |целое число |Нет |

### <a name="object"></a>Объект
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| email_address |string |Нет |
| email_type |string |Нет |
| status |string |Да |
| merge_fields |не определено |Нет |
| interests |string |Нет |
| язык |string |Нет |
| виртуальный IP-адрес |Логическое |Нет |
| location |не определено |Нет |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| members |array |Нет |
| list_id |string |Нет |
| total_items |целое число |Нет |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |string |Да |
| email_address |string |Да |
| unique_email_id |string |Нет |
| email_type |string |Нет |
| status |string |Нет |
| merge_fields |не определено |Да |
| interests |string |Нет |
| stats |не определено |Нет |
| ip_signup |string |Нет |
| timestamp_signup |string |Нет |
| ip_opt |string |Нет |
| timestamp_opt |string |Нет |
| member_rating |целое число |Нет |
| last_changed |string |Нет |
| язык |string |Нет |
| виртуальный IP-адрес |Логическое |Нет |
| email_client |string |Нет |
| location |не определено |Нет |
| last_note |не определено |Нет |
| list_id |string |Нет |
| _links |array |Нет |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->



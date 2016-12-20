---
title: " Использование соединителя Slack в приложениях логики | Документация Майкрософт"
description: "Начало использования соединителя Slack в приложениях логики службы приложений Microsoft Azure"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cacaa4a4a0089e1c3df54ae466af251026e1a55b


---
# <a name="get-started-with-the-slack-connector"></a>Начало работы с соединителем Slack
Slack — это средство для организации взаимодействия между группами пользователей, которое объединяет все сообщения в одном месте, поддерживает возможности мгновенного поиска и доступно в любом месте.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

С помощью соединителя Slack вы можете:

* создавать приложения логики.

Сведения о добавлении операции в приложения логики см. в статье [Создание нового приложения логики, подключающего службы SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Поговорим о триггерах и действиях
Соединитель Slack можно использовать в качестве действия. Триггеры отсутствуют. Все соединители поддерживают данные в форматах JSON и XML. 

 Соединитель Slack предоставляет следующие триггеры и действия:

### <a name="slack-actions"></a>Действия Slack
Вы можете выполнять перечисленные ниже действия:

| Действие | Описание |
| --- | --- |
| PostMessage |Публикация сообщения в указанном канале. |

## <a name="create-a-connection-to-slack"></a>Создание подключения к Slack
Чтобы использовать соединитель Slack, сначала нужно создать **подключение** , а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | Описание |
| --- | --- | --- |
| токен |Да |Указание учетных данных Slack |

Выполните следующие действия, чтобы войти в Slack и настроить **подключение** Slack в приложении логики:

1. Выберите **Повторение**
2. Выберите **частоту** и введите **интервал**.
3. Выберите **Добавить действие**.  
   ![Настройка Slack][1]  
4. В поле поиска введите "Slack" и дождитесь возвращения всех записей с "Slack" в имени.
5. Выберите **Slack — опубликовать сообщение**
6. Выберите **Вход в Slack**.  
   ![Настройка Slack][2]
7. Укажите учетные данные Slack, чтобы войти и авторизовать приложение.    
   ![Настройка Slack][3]  
8. Вы будете перенаправлены на страницу входа своей организации. **Разрешите** Slack взаимодействие с вашим приложением логики.      
   ![Настройка Slack][5] 
9. После успешной аутентификации вы перейдете к своему приложению логики, чтобы завершить его, настроив параметры в разделе **Slack —получение всех сообщений** . Добавьте другие необходимые вам триггеры и действия.  
   ![Настройка Slack][6]
10. Сохраните результаты работы, выбрав **Сохранить** в строке меню вверху.

> [!TIP]
> Это подключение можно использовать в других приложениях логики.
> 
> 

## <a name="slack-rest-api-reference"></a>Справочник по API REST Slack
#### <a name="this-documentation-is-for-version-10"></a>Эта документация предназначена для версии 1.0
### <a name="post-a-message-to-a-specified-channel"></a>Публикация сообщения в указанном канале.
**```POST: /chat.postMessage```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| channel |string |Да |запрос |Нет |Канал, частная группа или канал обмена мгновенными сообщениями, в которые отправляются сообщения. Этом может быть имя (например #general) или закодированный идентификатор. |
| text |string |Да |запрос |Нет |Текст отправляемого сообщения. Параметры форматирования описаны на странице https://api.slack.com/docs/formatting. |
| username |string |Нет |запрос |Нет |Имя программы-робота |
| as_user |Логическое |Нет |запрос |Нет |Передача значения "true", если публикация сообщения осуществляется прошедшим проверку пользователем, а не программой-роботом |
| parse |string |Нет |запрос |Нет |Изменение порядка обработки сообщений. Дополнительные сведения см. на странице https://api.slack.com/docs/formatting. |
| link_names |целое число |Нет |запрос |Нет |Поиск и связывание имен каналов и имен пользователей. |
| unfurl_links |Логическое |Нет |запрос |Нет |Передача значения "true" для включения развертывания главным образом текстового содержимого. |
| unfurl_media |Логическое |Нет |запрос |Нет |Передача значения "false" для отключения развертывания мультимедийного содержимого. |
| icon_url |string |Нет |запрос |Нет |URL-адрес изображения, которое будет использоваться в качестве значка для данного сообщения |
| icon_emoji |string |Нет |запрос |Нет |Символ эмодзи, который будет использоваться в качестве значка для данного сообщения |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 408 |Истекло время ожидания запроса |
| 429 |Слишком много запросов |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| 503 |Служба Slack недоступна |
| 504 |Истекло время ожидания шлюза |
| по умолчанию |Операция завершилась ошибкой. |

- - -
## <a name="object-definitions"></a>Определения объектов:
 **Message**: сообщение Yammer.

Обязательные свойства для Message:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| id |целое число |
| content_excerpt |string |
| sender_id |целое число |
| replied_to_id |целое число |
| created_at |string |
| network_id |целое число |
| message_type |string |
| sender_type |string |
| url |string |
| web_url |string |
| group_id |целое число |
| текст |не определено |
| thread_id |целое число |
| direct_message |Логическое |
| client_type |string |
| client_url |string |
| язык |string |
| notified_user_ids |array |
| privacy |string |
| liked_by |не определено |
| system_message |Логическое |

 **PostOperationRequest**: представляет запрос к соединителю Yammer на публикацию в Yammer.

Обязательные свойства для PostOperationRequest:

текст

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| текст |string |
| group_id |целое число |
| replied_to_id |целое число |
| direct_to_id |целое число |
| broadcast |Логическое |
| topic1 |string |
| topic2 |string |
| topic3 |string |
| topic4 |string |
| topic5 |string |
| topic6 |string |
| topic7 |string |
| topic8 |string |
| topic9 |string |
| topic10 |string |
| topic11 |string |
| topic12 |string |
| topic13 |string |
| topic14 |string |
| topic15 |string |
| topic16 |string |
| topic17 |string |
| topic18 |string |
| topic19 |string |
| topic20 |string |

 **MessageList**: список сообщений.

Обязательные свойства для MessageList:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| messages |array |

 **MessageBod**: текст сообщения.

Обязательные свойства для MessageBody:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| parsed |string |
| plain |string |
| rich |string |

 **LikedBy**: понравилось пользователям.

Обязательные свойства для LikedBy:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| count |целое число |
| names |array |

 **YammmerEntity**: понравилось пользователям.

Обязательные свойства для YammmerEntity:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| type |string |
| id |целое число |
| full_name |string |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)

## <a name="object-definitions"></a>Определения объектов:
 **WebResultModel**: результаты веб-поиска Bing.

Обязательные свойства для WebResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Название |string |
| Описание |string |
| DisplayUrl |string |
| id |string |
| FullUrl |string |

 **VideoResultModel**: результаты поиска видео в Bing.

Обязательные свойства для VideoResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Название |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| Среда выполнения |целое число |
| Эскиз |не определено |

 **ThumbnailModel**: свойства эскиза элемента мультимедиа.

Обязательные свойства для ThumbnailModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| MediaUrl |string |
| ContentType |string |
| Ширина |целое число |
| Высота |целое число |
| FileSize |целое число |

 **ImageResultModel**: результаты поиска изображения в Bing.

Обязательные свойства для ImageResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Название |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| SourceUrl |string |
| Эскиз |не определено |

 **NewsResultModel**: результаты поиска новостей в Bing.

Обязательные свойства для NewsResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Название |string |
| Описание |string |
| DisplayUrl |string |
| id |string |
| Источник |string |
| Дата |string |

 **SpellResultModel**: результаты вариантов написания при проверке орфографии в Bing.

Обязательные свойства для SpellResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| id |string |
| Значение |string |

 **RelatedSearchResultModel**: связанные результаты поиска Bing.

Обязательные свойства для RelatedSearchResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Название |string |
| id |string |
| BingUrl |string |

 **CompositeSearchResultModel**: результаты составного поиска Bing

Обязательные свойства для CompositeSearchResultModel:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| WebResultsTotal |целое число |
| ImageResultsTotal |целое число |
| VideoResultsTotal |целое число |
| NewsResultsTotal |целое число |
| SpellSuggestionsTotal |целое число |
| WebResults |array |
| ImageResults |array |
| VideoResults |array |
| NewsResults |array |
| SpellSuggestionResults |array |
| RelatedSearchResults |array |

## <a name="object-definitions"></a>Определения объектов:
 **PostOperationResponse**: представляет ответ операции публикации соединителя Slack для публикации в Slack.

Обязательные свойства для PostOperationResponse:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| ОК |Логическое |
| channel |string |
| ts |string |
| Message |не определено |
| error |string |

 **MessageItem**: сообщение канала.

Обязательные свойства для MessageItem:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| text |string |
| id |string |
| user |string |
| created |целое число |
| is_user-deleted |Логическое |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png



<!--HONumber=Nov16_HO3-->



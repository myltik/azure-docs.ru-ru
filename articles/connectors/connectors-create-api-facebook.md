---
title: "Добавление соединителя Facebook в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Facebook с параметрами интерфейса API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 7ac9d7eb9e322d8c44434add381030b51c9e9a4b


---
# <a name="get-started-with-the-facebook-connector"></a>Начало работы с соединителем Facebook
Подключение к Facebook позволяет оставлять публикации в хронике, получать канал страниц и выполнять другие действия. 

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

С помощью Facebook можно:

* формировать бизнес-процессы на основе данных, получаемых из Facebook; 
* использовать триггер при получении новой публикации;
* использовать действия, оставляющие публикации в хронике, получающие канал страниц и выполняющие другие действия. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно настроить трансляцию всех публикаций, которые появляются в хронике, в Twitter. 

Сведения о добавлении операции в приложения логики см. в статье [Создание нового приложения логики, подключающего службы SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель Facebook предоставляет следующие триггеры и действия. 

| триггеры; | Действия |
| --- | --- |
| <ul><li>При появлении новой публикации в хронике</li></ul> |<ul><li>Получение потока из хроники</li><li>Публикация в хронике</li><li>При появлении новой публикации в хронике</li><li>Получение канала страниц</li><li>Получение временной шкалы пользователя</li><li>Публикация на странице</li></ul> |

Все соединители поддерживают данные в форматах JSON и XML.

## <a name="create-a-connection-to-facebook"></a>Создание подключения к Facebook
При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Facebook.

1. Вход в учетную запись Facebook
2. Выберите **Авторизовать**и разрешите приложениям логики подключаться к Facebook и использовать его. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> Подключение к Facebook можно также использовать в других приложениях логики.
> 
> 

## <a name="swagger-rest-api-reference"></a>Справочник по REST API Swagger
Относится к версии 1.0.

### <a name="get-feed-from-my-timeline"></a>Получение потока из хроники
Получает веб-канал из хроники вошедшего в систему пользователя.  
```GET: /me/feed```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| fields |string |Нет |запрос |Нет |Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото). |
| ограничение |целое число |Нет |запрос |Нет |Максимальное количество публикаций для извлечения |
| на |string |Нет |запрос |Нет |Ограничение списка публикаций только теми, в которых указано местонахождение. |
| фильтр |string |Нет |запрос |Нет |Получение только тех публикаций, которые соответствуют определенному фильтру потока. |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="post-to-my-timeline"></a>Публикация в хронике
Публикует сообщение о состоянии в хронике пользователя, вошедшего в систему.  
```POST: /me/feed```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| post |string |Да |текст |Нет |Новое сообщение для публикации |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>При появлении новой публикации в хронике
Активирует новый поток, когда в хронике пользователя, вошедшего в систему, появляется новая публикация.  
```GET: /trigger/me/feed```

Параметров нет. 

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="get-page-feed"></a>Получение канала страниц
Получает сообщения из веб-канала указанной страницы.  
```GET: /{pageId}/feed```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Да |path |Нет |Идентификатор страницы, с которой нужно получать сообщения. |
| ограничение |целое число |Нет |запрос |Нет |Максимальное количество публикаций для извлечения |
| include_hidden |Логическое |Нет |запрос |Нет |Включение или отключение скрытых страницей публикаций |
| fields |string |Нет |запрос |Нет |Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="get-user-timeline"></a>Получение временной шкалы пользователя
Получает публикации из хроники пользователя.  
```GET: /{userId}/feed```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| userId |строка |Да |path |Нет |Идентификатор пользователя, хронику которого нужно извлечь. |
| ограничение |целое число |Нет |запрос |Нет |Максимальное количество публикаций для извлечения |
| на |string |Нет |запрос |Нет |Ограничение списка публикаций только теми, в которых указано местонахождение. |
| фильтр |string |Нет |запрос |Нет |Получение только тех публикаций, которые соответствуют определенному фильтру потока. |
| fields |string |Нет |запрос |Нет |Укажите поля, которые необходимо вернуть. Пример (идентификатор, имя, фото). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

### <a name="post-to-page"></a>Публикация на странице
Публикует сообщение на странице Facebook от имени вошедшего в систему пользователя.  
```POST: /{pageId}/feed```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Да |path |Нет |Идентификатор страницы для публикации. |
| post |many |Да |текст |Нет |Новое сообщение для публикации. |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
#### <a name="getfeedresponse"></a>GetFeedResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| data |array |Нет |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| data |array |Нет |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: отдельная запись в канале профиля
В качестве профиля может выступать пользователь, страница, приложение или группа. 

| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |
| admin_creator |array |Нет |
| caption |string |Нет |
| created_time |string |Нет |
| Описание |строка |Нет |
| feed_targeting |не определено |Нет |
| from |не определено |Нет |
| icon |string |Нет |
| is_hidden |Логическое |Нет |
| is_published |Логическое |Нет |
| link |string |Нет |
| message |string |Нет |
| Имя |строка |Нет |
| object_id |string |Нет |
| picture |string |Нет |
| place |не определено |Нет |
| privacy |не определено |Нет |
| properties |array |Нет |
| источник |строка |Нет |
| status_type |string |Нет |
| story |string |Нет |
| targeting |не определено |Нет |
| значение |array |Нет |
| type |строка |Нет |
| updated_time |string |Нет |
| with_tags |не определено |Нет |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: отдельная запись в канале профиля
В качестве профиля может выступать пользователь, страница, приложение или группа.

| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |
| created_time |string |Нет |
| from |не определено |Нет |
| message |string |Нет |
| type |строка |Нет |

#### <a name="adminitem"></a>AdminItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |
| link |string |Нет |

#### <a name="propertyitem"></a>PropertyItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Имя |строка |Нет |
| text |string |Нет |
| href |string |Нет |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| message |string |Да |
| link |string |Нет |
| picture |string |Нет |
| Имя |строка |Нет |
| caption |string |Нет |
| Описание |строка |Нет |
| place |string |Нет |
| tags |string |Нет |
| privacy |не определено |Нет |
| object_attachment |string |Нет |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| message |string |Да |
| link |string |Нет |
| picture |string |Нет |
| Имя |строка |Нет |
| caption |string |Нет |
| Описание |строка |Нет |
| Действия |array |Нет |
| place |string |Нет |
| tags |string |Нет |
| object_attachment |string |Нет |
| targeting |не определено |Нет |
| feed_targeting |не определено |Нет |
| published |Логическое |Нет |
| scheduled_publish_time |string |Нет |
| backdated_time |string |Нет |
| backdated_time_granularity |string |Нет |
| child_attachments |array |Нет |
| multi_share_end_card |Логическое |Нет |

#### <a name="postfeedresponse"></a>PostFeedResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |

#### <a name="profilecollection"></a>ProfileCollection
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| data |array |Нет |

#### <a name="useritem"></a>UserItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |
| first_name |string |Нет |
| last_name |string |Нет |
| Имя |строка |Нет |
| gender |string |Нет |
| about |string |Нет |

#### <a name="actionitem"></a>ActionItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Имя |строка |Нет |
| link |string |Нет |

#### <a name="targetitem"></a>TargetItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| countries |array |Нет |
| locales |array |Нет |
| regions |array |Нет |
| cities |array |Нет |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: объект, управляющий каналом новостей для этой публикации
Пользователи в этих группах, скорее всего, увидят эту публикацию, остальные с меньшей вероятностью. Применяется только к страницам.

| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| countries |array |Нет |
| regions |array |Нет |
| cities |array |Нет |
| age_min |целое число |Нет |
| age_max |целое число |Нет |
| genders |array |Нет |
| relationship_statuses |array |Нет |
| interested_in |array |Нет |
| college_years |array |Нет |
| interests |array |Нет |
| relevant_until |целое число |Нет |
| education_statuses |array |Нет |
| locales |array |Нет |

#### <a name="placeitem"></a>PlaceItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Нет |
| Имя |строка |Нет |
| overall_rating |number |Нет |
| location |не определено |Нет |

#### <a name="locationitem"></a>LocationItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| city |string |Нет |
| country |string |Нет |
| latitude |number |Нет |
| located_in |string |Нет |
| longitude |number |Нет |
| Имя |строка |Нет |
| region |строка |Нет |
| state |string |Нет |
| street |string |Нет |
| zip |string |Нет |

#### <a name="privacyitem"></a>PrivacyItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Описание |строка |Нет |
| value |string |Да |
| allow |string |Нет |
| deny |string |Нет |
| friends |string |Нет |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| link |string |Нет |
| picture |string |Нет |
| image_hash |string |Нет |
| Имя |строка |Нет |
| Описание |строка |Нет |

#### <a name="postphotorequest"></a>PostPhotoRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| url |string |Да |
| caption |string |Нет |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Да |
| post_id |string |Да |

#### <a name="postvideorequest"></a>PostVideoRequest
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| videoData |string |Да |
| Описание |string |Да |
| title |string |Да |
| uploadedVideoName |string |Нет |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| data |не определено |Да |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| url |string |Да |
| is_silhouette |Логическое |Да |
| height |string |Нет |
| width |string |Нет |

#### <a name="geteventresponse"></a>GetEventResponse
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| data |array |Да |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| id |строка |Да |
| Имя |string |Да |
| start_time |string |Нет |
| end_time |string |Нет |
| timezone |string |Нет |
| location |string |Нет |
| Описание |строка |Нет |
| ticket_uri |string |Нет |
| rsvp_status |string |Да |

## <a name="next-steps"></a>Дальнейшие действия
[Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->



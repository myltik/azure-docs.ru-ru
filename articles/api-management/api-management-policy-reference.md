---
title: "Справочник по политикам службы управления Azure API "
description: "Сведения о политиках, доступных для настройки службы управления API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97a6f253eda39d0573a2384691a6b294c3894c25


---
# <a name="azure-api-management-policy-reference"></a>Справочник по политикам службы управления Azure API 
Этот раздел содержит индекс политик, перечисленных в [справочнике по политикам управления интерфейсами API][Справочник по политикам управления API]. Дополнительные сведения о добавлении и настройке политик см. в статье [Политики в управлении API][Политики в управлении API].

Выражения политики можно использовать в качестве значений атрибутов или текстовых значений в любой политике управления API, если в ней не указано иное. Некоторые политики (в том числе [управления последовательностью][Управление потоками] и [настройки переменной][Задание переменной]) основаны на выражениях политик. Дополнительные сведения см. в разделах [Расширенные политики][Расширенные политики] и [Выражения политики][Выражения политики].

## <a name="policy-reference-index"></a>Справочные указатели по политикам
* [Политики ограничения доступа][Политики ограничения доступа]
  * [Проверка заголовка HTTP][Проверка заголовка HTTP] — обеспечивает принудительное добавление заголовка HTTP и (или) его значения.
  * [Ограничение частоты вызовов по подписке][Ограничение частоты вызовов по подписке] — предотвращает пики использования API, ограничивая частоту вызовов для каждой подписки.
  * [Ограничение частоты вызовов по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) — предотвращает пики использования API, ограничивая частоту вызовов по ключу.
  * [Ограничение IP-адресов вызывающих объектов][Ограничение IP-адресов вызывающих объектов] — фильтрует (разрешает или запрещает) вызовы с конкретных IP-адресов и (или) диапазонов адресов.
  * [Задание квоты использования по подписке][Задание квоты использования по подписке] — позволяет принудительно устанавливать возобновляемую или действующую в течение времени существования квоту числа вызовов и (или) квоту пропускной способности для каждой подписки.
  * [Задание квоты использования по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа.
  * [Проверка JWT][Проверка JWT] — обеспечивает принудительное задание и проверку JWT, извлеченного из заданного заголовка HTTP или параметра запроса.
* [Расширенные политики][Расширенные политики]
  * [Управление потоками][Управление потоками] — условно применяет правила политики на основе результатов вычисления логических [выражений][выражений].
  * [Перенаправляющий запрос][Перенаправляющий запрос] — перенаправляет запрос во внутреннюю службу.
  * [Регистрировать в концентраторе событий][Регистрировать в концентраторе событий] — отправляет сообщения в указанном формате объекту, который определен сущностью [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Повторить](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) — повторяет выполнение инструкций встраиваемой политики, если не выполнено условие и до тех пор пока оно не будет выполнено. Выполнение будет повторяться через определенные промежутки времени и до указанного количества повторных попыток.
  * [Возврат ответа](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) — прекращает выполнение конвейера и возвращает указанный ответ непосредственно вызывающему объекту.
  * [Отправка одностороннего запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) — отправляет запрос на указанный URL-адрес и не ожидает ответа.
  * [Отправка запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) — отправляет запрос на указанный URL-адрес.
  * [Установка метода запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) — позволяет изменить метод HTTP для запроса.
  * [Установка кода состояния](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) — меняет код состояния HTTP на указанное значение.
  * [Задание переменной][Задание переменной] — сохраняет значение в именованной переменной [контекста][контекста] для последующего использования.
  * [Трассировка](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) — добавляет строку в выходные данные [инспектора API](api-management-howto-api-inspector.md).
  * [Ожидание](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) — ожидает вложенный запрос отправки, получения значения из кэша или завершения политик управления потоком перед продолжением.
* [Политики аутентификации][Политики аутентификации]
  * [Обычная проверка подлинности][Обычная проверка подлинности] — обычная проверка подлинности во внутренней службе.
  * [Аутентификация с помощью сертификата клиента][Аутентификация с помощью сертификата клиента] — аутентификация внутренней службы с помощью сертификатов клиентов.
* [Политики кэширования][Политики кэширования] 
  * [Получение из кэша][Получение из кэша] — выполнение операции поиска в кэше и возврат допустимого кэшированного ответа при его наличии.
  * [Сохранение в кэше][Сохранение в кэше] — помещение в кэш ответа в соответствии с заданной конфигурацией управления кэшем.
  * [Получение значения из кэша](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) — получение кэшированного элемента по ключу.
  * [Сохранение значения в кэше](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) — сохранение элемента в кэше по ключу.
  * [Удалить значение из кэша](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) — удаление элемента в кэше по ключу.
* [Кроссдоменные политики][Кроссдоменные политики] 
  * [Разрешение кросс-доменных вызовов][Разрешение кросс-доменных вызовов] — делает API доступным из браузерных клиентов Adobe Flash и Microsoft Silverlight.
  * [CORS][CORS] — добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов.
  * [JSONP][JSONP] — добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов JavaScript.
* [Политики преобразования][Политики преобразования] 
  * [Преобразование JSON в XML][Преобразование JSON в XML] — преобразовывает текст запроса или ответа в формате JSON в формат XML.
  * [Преобразование XML в JSON][Преобразование XML в JSON] — преобразовывает текст запроса или ответа в формате XML в формат JSON.
  * [Поиск и замена строки в тексте][Поиск и замена строки в тексте] — позволяет найти подстроку запроса или ответа и заменяет ее другой подстрокой.
  * [Маскировка URL-адресов в содержимом][Маскировка URL-адресов в содержимом] — перезаписывает (маскирует) ссылки в тексте ответа так, чтобы каждая из них указывала на эквивалентную ссылку через шлюз.
  * [Задание внутренней службы][Задание внутренней службы] — изменяет внутреннюю службу для входящего запроса.
  * [Задание текста][Задание текста] — задает текст сообщения для входящих и исходящих запросов.
  * [Установка HTTP-заголовка][Установка HTTP-заголовка] — присваивает значение существующему заголовку ответа и (или) запроса либо добавляет новый заголовок ответа и (или) запроса.
  * [Настройка параметра строки запроса][Настройка параметра строки запроса] — позволяет добавить, удалить параметр строки запроса или изменить его значение.
  * [Перезапись URL-адреса][Перезапись URL-адреса] — преобразовывает URL-адрес запроса из его общедоступной формы в форму, ожидаемую веб-службой.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о выражениях политики см. в следующем видео.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Политики ограничения доступа]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Проверка заголовка HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Ограничение частоты вызовов по подписке]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Ограничение IP-адресов вызывающих объектов]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Задание квоты использования по подписке]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Проверка JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Расширенные политики]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Управление потоками]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Задание переменной]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[выражений]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[контекста]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Перенаправляющий запрос]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Регистрировать в концентраторе событий]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Политики аутентификации]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Обычная проверка подлинности]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Аутентификация с помощью сертификата клиента]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Политики кэширования]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Получение из кэша]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Сохранение в кэше]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Кроссдоменные политики]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Разрешение кросс-доменных вызовов]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Политики преобразования]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Преобразование JSON в XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Преобразование XML в JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Поиск и замена строки в тексте]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Маскировка URL-адресов в содержимом]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Задание внутренней службы]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Задание текста]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Установка HTTP-заголовка]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Настройка параметра строки запроса]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Перезапись URL-адреса]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Политики в управлении API]: api-management-howto-policies.md
[Справочник по политикам управления API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Выражения политики]: https://msdn.microsoft.com/library/azure/dn910913.aspx





<!--HONumber=Nov16_HO3-->



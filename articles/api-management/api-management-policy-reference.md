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
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Справочник по политикам службы управления Azure API 
Этот раздел содержит указатель политик, перечисленных в [справочнике по политикам службы управления API][API Management policy reference]. Дополнительные сведения о добавлении и настройке политик см. в статье о [политиках в службе управления API][Policies in API Management].

Выражения политики можно использовать в качестве значений атрибутов или текстовых значений в любой политике управления API, если в ней не указано иное. Некоторые политики (включая [Поток управления][Control flow] и [Задание переменной][Set variable]) основаны на выражениях политики. Чтобы узнать больше, ознакомьтесь с [расширенными политиками][Advanced policies] и [выражениями политики][Policy expressions].

## <a name="policy-reference-index"></a>Справочные указатели по политикам
* [Политики ограничения доступа][Access restriction policies]
  * [Проверка заголовка HTTP][Check HTTP header] — обеспечивает принудительный ввод заголовка HTTP и (или) его значения.
  * [Ограничение частоты вызовов по подписке][Limit call rate by subscription] — предотвращает пики использования API, ограничивая частоту вызовов для каждой подписки.
  * [Ограничение частоты вызовов по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) — предотвращает пики использования API, ограничивая частоту вызовов по ключу.
  * [Ограничение IP-адресов вызывающих объектов][Restrict caller IPs] — фильтрует (разрешает или запрещает) вызовы с конкретных IP-адресов и (или) диапазонов адресов.
  * [Задание квоты использования по подписке][Set usage quota by subscription] — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждой подписки.
  * [Задание квоты использования по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа.
  * [Проверка JWT][Validate JWT] — обеспечивает принудительное задание и проверку маркера JWT, извлеченного из заданного заголовка HTTP или параметра запроса.
* [Расширенные политики][Advanced policies]
  * [Поток управления][Control flow] — условно применяет правила политики на основе результатов вычисления логических [выражений][expressions].
  * [Перенаправляющий запрос][Forward request] — перенаправляет запрос во внутреннюю службу.
  * [Регистрация в концентраторе событий][Log to Event Hub] — отправляет сообщения в определенном формате объекту, который указан сущностью [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Повторить](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) — повторяет выполнение инструкций встраиваемой политики, если не выполнено условие и до тех пор пока оно не будет выполнено. Выполнение будет повторяться через определенные промежутки времени и до указанного количества повторных попыток.
  * [Возврат ответа](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) — прекращает выполнение конвейера и возвращает указанный ответ непосредственно вызывающему объекту.
  * [Отправка одностороннего запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) — отправляет запрос на указанный URL-адрес и не ожидает ответа.
  * [Отправка запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) — отправляет запрос на указанный URL-адрес.
  * [Установка метода запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) — позволяет изменить метод HTTP для запроса.
  * [Установка кода состояния](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) — меняет код состояния HTTP на указанное значение.
  * [Задание переменной][Set variable] — сохраняет значение в именованной переменной [контекста][context] для последующего использования.
  * [Трассировка](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) — добавляет строку в выходные данные [инспектора API](api-management-howto-api-inspector.md).
  * [Ожидание](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) — ожидает вложенный запрос отправки, получения значения из кэша или завершения политик управления потоком перед продолжением.
* [Политики аутентификации][Authentication policies]
  * [Обычная проверка подлинности][Authenticate with Basic] – обычная проверка подлинности внутренней службы.
  * [Аутентификация с помощью сертификата клиента][Authenticate with client certificate] – аутентификация внутренней службы с помощью сертификатов клиентов.
* [Политики кэширования][Caching policies] 
  * [Получение из кэша][Get from cache] — выполнение поиска в кэше и возврат допустимого кэшированного ответа при его наличии.
  * [Сохранение в кэше][Store to cache] — помещение в кэш ответа в соответствии с заданной конфигурацией управления кэшем.
  * [Получение значения из кэша](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) — получение кэшированного элемента по ключу.
  * [Сохранение значения в кэше](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) — сохранение элемента в кэше по ключу.
  * [Удалить значение из кэша](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) — удаление элемента в кэше по ключу.
* [Междоменные политики][Cross domain policies] 
  * [Разрешение междоменных вызовов][Allow cross-domain calls] — делает API доступным из браузерных клиентов Adobe Flash и Microsoft Silverlight.
  * [CORS][CORS] — добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов.
  * [JSONP][JSONP] — добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить междоменные вызовы из браузерных клиентов JavaScript.
* [Политики преобразования][Transformation policies] 
  * [Преобразование JSON в XML][Convert JSON to XML] — преобразует текст запроса или ответа в формате JSON в формат XML.
  * [Преобразование XML в JSON][Convert XML to JSON] — преобразует текст запроса или ответа в формате XML в формат JSON.
  * [Поиск и замена строки в тексте][Find and replace string in body] — позволяет найти подстроку запроса или ответа и заменить ее на другую подстроку.
  * [Маскирование URL-адресов в содержимом][Mask URLs in content] — перезаписывает (маскирует) ссылки в тексте ответа так, чтобы каждая из них указывала на эквивалентную ссылку через шлюз.
  * [Задание внутренней службы][Set backend service] — изменяет внутреннюю службу для входящего запроса.
  * [Задание текста][Set body] — задает текст сообщения для входящих и исходящих запросов.
  * [Задание заголовка HTTP][Set HTTP header] — присваивает значение существующему заголовку ответа и (или) запроса либо добавляет новый заголовок ответа и (или) запроса.
  * [Настройка параметра строки запроса][Set query string parameter] — добавляет, заменяет значение или удаляет параметр строки запроса.
  * [Перезапись URL-адреса][Rewrite URL] — преобразовывает URL-адрес запроса из его общедоступной формы в форму, ожидаемую веб-службой.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о выражениях политики см. в следующем видео.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx



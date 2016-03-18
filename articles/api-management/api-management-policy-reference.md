<properties 
	pageTitle="Справочник по политикам службы управления Azure API " 
	description="Сведения о политиках, доступных для настройки службы управления API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="sdanie"/>

# Справочник по политикам службы управления Azure API

В этом разделе содержится ссылка на политики [Справка по политике управления интерфейсами API][]. Дополнительные сведения о добавлении и настройке политик см. в разделе [Политики службы управления API][].

Выражения политики можно использовать в качестве значений атрибутов или текстовых значений в любой политике управления API, если в ней не указано иное. Некоторые политики, например [Управление потоками][] и [Задание переменной][], основаны на выражениях политики. Дополнительную информацию см. в разделах [Расширенные политики][] и [Выражения политики][].

## Справочные указатели по политикам

-	[Политики ограничения доступа][]
	-	[Проверка заголовка HTTP][] – обеспечивает принудительный ввод заголовка HTTP и/или его значения.
	-	[Ограничение частоты вызовов по подписке][] — предотвращает пики использования API, ограничивая частоту вызовов для каждой подписки.
	-	[Ограничение частоты вызовов по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) — предотвращает пики использования API, ограничивая частоту вызовов по ключу.
	-	[Ограничение IP-адресов вызывающих объектов][] – фильтрует (разрешает или запрещает) вызовы с конкретных IP-адресов и/или диапазонов адресов.
	-	[Задание квоты использования по подписке][] — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждой подписки.
	-	[Задание квоты использования по ключу](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) — позволяет принудительно устанавливать возобновляемую или действующую в течение срока службы квоту на число вызовов и (или) квоту пропускной способности для каждого ключа.
	-	[Проверка JWT][] – обеспечивает принудительное задание и проверку JWT, извлеченного из заданного заголовка HTTP или параметра запроса.
-	[Расширенные политики][]
	-	[Управление потоками][] – условно применяет правила политики на основе результатов вычисления логических [выражений][].
	-	[Перенаправляющий запрос][] — перенаправляет запрос в серверную службу.
	-	[Регистрировать в концентраторе событий][] — отправляет сообщения в определенном формате объекту, который указан [средством ведения журнала](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
	-	[Возврат ответа](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) — прекращает выполнение конвейера и возвращает указанный ответ непосредственно вызывающему объекту.
	-	[Отправка одностороннего запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) — отправляет запрос на указанный URL-адрес и не ожидает ответа.
	-	[Отправка запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) — отправляет запрос на указанный URL-адрес.
	-	[Установка метода запроса](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) — позволяет изменить метод HTTP для запроса.
	-	[Установка кода состояния](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) — меняет код состояния HTTP на указанное значение.
	-	[Установка переменной][] – сохраняет значение в именованной переменной [контекста][] для последующего использования.
	-	[Ожидание](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) — ожидает вложенного запроса отправки, получения значения из кэша или завершения политик управления потоком перед продолжением.
-	[Политики аутентификации][]
	-	[Обычная проверка подлинности][] – обычная проверка подлинности внутренней службы.
	-	[Аутентификация с помощью сертификата клиента][] – аутентификация внутренней службы с помощью сертификатов клиентов.
-	[Политики кэширования][] 
	-	[Получение из кэша][] – выполнение операции поиска в кэше и возврат допустимого кэшированного ответа при его наличии.
	-	[Сохранение в кэше][] – помещение в кэш ответа в соответствии с заданной конфигурацией управления кэшем.
	-	[Получение значения из кэша](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) — получение кэшированного элемента по ключу.
	-	[Сохранение значения в кэше](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) — сохранение элемента в кэше по ключу.
-	[Кроссдоменные политики][] 
	-	[Разрешение кросс-доменных вызовов][] – делает API доступным из клиентов на основе браузеров Adobe Flash и Microsoft Silverlight.
	-	[CORS][] – добавляет поддержку общего доступа к ресурсам независимо от источника (CORS) для операции или API, чтобы разрешить кросс-доменные вызовы от клиентов на основе браузера.
	-	[JSONP][] – добавляет поддержку JSON с заполнением (JSONP) для операции или API, чтобы разрешить кросс-доменные вызовы из браузерных клиентов JavaScript.
-	[Политики преобразования][] 
	-	[Преобразование JSON в XML][] – преобразует текст запроса или ответа в формате JSON в формат XML.
	-	[Преобразование XML в JSON][] – преобразует текст запроса или ответа в формате XML в формат JSON.
	-	[Поиск и замена строки в тексте][] – отыскивает подстроку запроса или ответа и заменяет ее на другую подстроку.
	-	[Маскировка URL-адресов в содержимом][] — перезаписывает (маскирует) ссылки в тексте ответа и в заголовке Location так, чтобы они указывали на эквивалентную ссылку через шлюз.
	-	[Задание внутренней службы][] – изменяет внутреннюю службу для входящего запроса.
	-	[Задание текста][] – задает текст сообщения для входящих и исходящих запросов.
	-	[Установка HTTP-заголовка][] -– назначает значение существующему заголовку ответа и/или запроса или добавляет новый заголовок ответа и/или запроса.
	-	[Настройка параметра строки запроса][] - добавляет, заменяет значение или удаляет параметр строки запроса.
	-	[Перезапись URL-адреса][] – преобразует URL-адрес запроса из его общедоступной формы в форму, ожидаемую веб-службой.

## Дальнейшие действия

Дополнительную информацию о выражениях политики см. в следующем видео.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Политики ограничения доступа]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Проверка заголовка HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Ограничение частоты вызовов по подписке]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Ограничение IP-адресов вызывающих объектов]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Задание квоты использования по подписке]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Проверка JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Расширенные политики]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Управление потоками]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Задание переменной]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Установка переменной]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
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



[Политики службы управления API]: api-management-howto-policies.md
[Справка по политике управления интерфейсами API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Выражения политики]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=AcomDC_0218_2016-->
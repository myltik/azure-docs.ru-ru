---
title: "Как добавлять операции в API в управлении API Azure | Документация Майкрософт"
description: "Добавление операций в API в службе управления API Azure"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1bc391f5be5e41c6b6691950241ba5f3abdbf538


---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Добавление операций в API в Azure API Management
Прежде чем API может быть использован в API Management, должны быть добавлены операции. Это руководство описывает добавление и настройку операций различного типа в интерфейсе API в API Management.

## <a name="add-operation"> </a>Добавление операции
Операции добавляются и настраиваются для API на портале издателя. Чтобы перейти на портал издателя, на портале Azure щелкните **Publisher portal** (Портал издателя) для службы управления API.

![Портал издателя][api-management-management-console]

> Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Создание экземпляра управления API] в статье [Начало работы со службой управления Azure API][Приступая к работе со службой управления API].
> 
> 

Выберите требуемый API на портале издателя, а затем перейдите на вкладку **Операции** . 

![Операции][api-management-operations]

Щелкните **Добавить операцию** , чтобы добавить новую операцию. Появится окно **Новая операция**, вкладка **Сигнатура** будет выбрана по умолчанию.

![Добавить операцию][api-management-add-operation]

Выберите **команду HTTP** в раскрывающемся списке.

![Метод HTTP][api-management-http-method]

<a name="url-template"></a>

Определите шаблон URL-адреса путем ввода фрагмента URL-адреса, который содержит один или несколько сегментов пути URL, а также несколько параметров строки запроса (либо вообще без параметров). Шаблон URL-адреса, добавляемый к основному URL-адресу API, идентифицирует одиночную HTTP-операцию. Он может содержать один или несколько частей переменной с именем, которые идентифицируются изогнутыми фигурными скобками. Эти части переменной называются параметрами шаблона и представляют собой динамически назначаемые значения, извлекаемые из URL-адреса запроса, когда запрос обрабатывается платформой API Management.

![Шаблон URL-адреса][api-management-url-template]

<a name="rewrite-url-template"></a>

При необходимости укажите **Шаблон URL-адреса перезаписи**. Это позволяет использовать стандартный шаблон URL-адреса для обработки входящих запросов на интерфейсной части при вызове серверной части с помощью преобразованного URL-адреса в соответствии с шаблоном перезаписи. В шаблоне перезаписи должны использоваться параметры шаблона из шаблона URL-адреса. В следующем пример показано, как тип контента, закодированный как сегмент пути в веб-службе из предыдущего примера, может быть введен в качестве параметра запроса в API, опубликованном на платформе API Management с помощью шаблонов URL-адреса.

![Перезапись шаблона URL-адреса][api-management-url-template-rewrite]

Вызывающие объекты операции будут использовать формат `/customers?customerid=ALFKI`, который будет сопоставляться с форматом `/Customers('ALFKI')` при вызове серверной службы.

Текстовые поля **Отображаемое имя** и **Описание** содержат описание операции и используются для предоставления документации разработчикам, которые используют этот API на портале разработчика.

![Описание][api-management-description]

Описание операции может быть введено в виде обычного текста или в формате HTML в текстовом поле **Описание** .

## <a name="operation-caching"> </a>Кэширование операций
Кэширование ответов уменьшает задержку для потребителей API, снижает потребляемую пропускную способность и сокращает нагрузку на веб-службу HTTP, которая реализует интерфейс API. 

Для легкого и быстрого включения кэширования для операции перейдите на вкладку **Кэширование** и установите флажок **Включить**.

![Caching][api-management-caching-tab]

**Длительность** указывает период времени, в течение которого ответ операции остается в кэше. Значение по умолчанию составляет 3600 секунд или 1 час.

Ключи кэша используются, чтобы отличать ответы друг от друга таким образом, чтобы ответ, соответствующий каждому отличному ключу кэша, получал свое собственное отдельное кэшированное значение. Или же можно ввести специальные параметры строки запроса и/или HTTP-заголовки, которые должны будут использоваться при вычислении значений ключей кэша, соответственно в текстовые поля **В зависимости от параметров строки запроса** и **Vary by headers** (В зависимости от заголовков). Если они не заданы, при создании ключей кэша используются полный URL-адрес запроса и следующие значения HTTP-заголовка: **Accept** и **Accept-Charset**.

> Дополнительные сведения о кэшировании и политиках кэширования см. в статье [Добавление кэширования для повышения производительности в службе управления API Azure][Добавление кэширования для повышения производительности в службе управления API Azure].
> 
> 

## <a name="request-parameters"> </a>Параметры запроса
Параметрами операции можно управлять на вкладке «Параметры». Параметры, заданные в поле **Шаблон URL-адреса** на вкладке **Сигнатура**, добавляются автоматически и могут быть изменены только путем редактирования шаблона URL-адреса. Дополнительные параметры можно вводить вручную.

Для добавления нового параметра запроса щелкните **Добавить параметр запроса** и введите следующие сведения.

* **Имя** - имя параметра.
* **Описание** - краткое описание параметра (необязательно).
* **Тип** - тип параметра, выбранный в раскрывающемся списке.
* **Значения** - значения, которые могут быть назначены этому параметру. Одно из значений может быть отмечено как значение по умолчанию (необязательно).
* **Обязательный** - установите флажок, если этот параметр должен быть обязательным. 

![Параметры запроса][api-management-request-parameters]

## <a name="request-body"> </a>Текст запроса
Если операция допускает (например, PUT, POST) и требует наличия тела, то можно ввести пример такого тела во всех поддерживаемых форматах представления (например, json, XML). 

> Тело запроса используется только в документационных целях и не проверяется.
> 
> 

Для ввода тела запроса перейдите на вкладку **Тело** .

Щелкните **Добавить представление**, начните вводить требуемое название типа контента (например, приложение/json), выберите его в раскрывающемся списке и вставьте требуемый пример тела запроса в выбранном формате в текстовое поле. 

![Текст запроса][api-management-request-body]

В дополнение к представлениям необязательное текстовое описание также можно ввести в текстовом поле **Описание** .

## <a name="responses"> </a>Ответы
Рекомендуется создавать примеры ответов для всех кодов состояния, которые может выдать операция. Каждый код состояния может содержать более одного примера содержимого ответа, по одному для каждого поддерживаемого типа содержимого. 

Для добавления ответа щелкните **Добавить** и начните вводить требуемый код состояния. В этом примере код состояния — **200 OK**. Как только код появится в раскрывающемся списке, выберите его. Будет создан код ответа, который будет добавлен в вашу операцию.

![Код ответа][api-management-response-code]

Щелкните **Добавить представление**, начните вводить требуемое название типа контента (например, приложение/json), а затем выберите его в раскрывающемся списке.

![Тип контента тела][api-management-response-body-content-type]

Вставьте пример тела ответа в выбранном формате в текстовое поле. 

![Тело ответа][api-management-response-body]

При необходимости добавьте необязательное описание в текстовое поле **Описание** .

После настройки операции щелкните **Сохранить**.

## <a name="next-steps"> </a>Дальнейшие действия
Как только операции будут добавлены в API, следующим шагом будет сопоставление интерфейса API с продуктом и его публикация, чтобы разработчики могли вызывать его операции.

* [Как создать и опубликовать продукт][Как создать и опубликовать продукт]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Добавление операции]: #add-operation
[Кэширование операций]: #operation-caching
[Параметры запроса]: #request-parameters
[Текст запроса]: #request-body
[Ответы]: #responses
[Дальнейшие действия]: #next-steps

[Приступая к работе со службой управления API]: api-management-get-started.md
[Создание экземпляра управления API]: api-management-get-started.md#create-service-instance

[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Добавление кэширования для повышения производительности в службе управления API Azure]: api-management-howto-cache.md



<!--HONumber=Nov16_HO3-->



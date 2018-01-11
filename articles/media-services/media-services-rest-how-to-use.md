---
title: "Обзор REST API операций служб мультимедиа | Документация Майкрософт"
description: "Обзор интерфейса REST API служб мультимедиа"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: 066959058576af830103aa98a12f0c36acfdbb14
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Обзор REST API операций служб мультимедиа
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

**REST операции служб мультимедиа** API используется для создания заданий, активы, каналы Live и другие ресурсы в учетной записи служб мультимедиа. Дополнительные сведения см. в статье [Media Services Operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) (Справочник по REST API операций служб мультимедиа).

Службы мультимедиа предоставляют API REST, который принимает JSON или atom + pub XML-формате. API REST служб мультимедиа требуются определенные заголовки HTTP, которые необходимо отправить каждый клиент при подключении служб мультимедиа, а также набор дополнительных заголовков. В следующих разделах описываются заголовки и HTTP-команды, которые можно использовать при создании запросов и получения ответов из служб мультимедиа.

Проверка подлинности для API REST Serivces мультимедиа осуществляется посредством проверки подлинности Azure Active Directory, которая описывается в статье [проверки подлинности используют Azure AD для доступа к API служб мультимедиа Azure с REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Рекомендации

При использовании REST следует принимать во внимание следующие соображения.

* При запросе сущностей существует ограничение в 1000 сущностей, возвращаемых за один раз, так как в открытой версии 2 REST количество результатов запросов ограничено 1000. Используйте **Skip** и **Take** (.NET) или **top** (REST), как описано в [этом](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) и [этом](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) примерах. 
* При использовании JSON и указание использовать **__metadata** ключевое слово в запросе (например, ссылку на связанный объект) необходимо установить **Accept** заголовок, чтобы [JSON в подробном формате](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(см. следующий пример). Odata "не поймет" свойство **__metadata** в запросе, если не задать для него подробный формат.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Стандартные заголовки HTTP-запроса, поддерживаемые службами мультимедиа
Для каждого вызова, отправляемого в службы мультимедиа, предусмотрен набор обязательных заголовков, которые необходимо включить в запрос, а также набор дополнительных заголовков, которые можно включить при необходимости. В следующей таблице перечислены необходимые заголовки.

| Заголовок | type | Значение |
| --- | --- | --- |
| Авторизация |Носитель |Носитель – единственный принимаемый механизм авторизации. Значение должно включать токен доступа, предоставленные Azure Active Directory. |
| x-ms-version |Decimal |2.17 (или самой последней версии)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Поскольку службы мультимедиа использует протокол OData для предоставления его API-интерфейсы REST, заголовки DataServiceVersion и MaxDataServiceVersion должны включаться во всех запросах; Тем не менее если это не так, затем в настоящее время службы мультимедиа принимает значение DataServiceVersion 3.0.
> 
> 

Ниже приведен набор дополнительных заголовков.

| Заголовок | type | Значение |
| --- | --- | --- |
| Дата |RFC 1123 date |Метка времени запроса |
| Принять |Тип содержимого |Запрошенный тип содержимого для ответа, например:<p> application/json;odata=verbose<p> application/atom+xml<p> Ответы могут содержать разные типы содержимого, например больших двоичных объектов, где успешный ответ содержит потока больших двоичных объектов в виде полезных данных. |
| Accept-Encoding |Gzip, deflate |Кодировка GZIP и, в соответствующих случаях, DEFLATE. Примечание. Для больших ресурсов службы мультимедиа могут пропустить этот заголовок и вернуть несжатые данные. |
| Accept-Language |"en", "ru" и т. д. |Указывает предпочитаемый язык для ответа. |
| Accept-Charset |Тип кодировки, например "UTF-8" |По умолчанию — UTF-8. |
| X-HTTP-Method |Метод HTTP |Позволяет клиентам или брандмауэрам, которые не поддерживают методы HTTP, например PUT или DELETE, использовать эти методы, туннелированные посредством вызова GET. |
| Content-Type |Тип содержимого |Тип содержимого текста запросов PUT или POST. |
| client-request-id |Строка |Определяемое вызывающим объектом значение, по которому можно идентифицировать данный запрос. Если это значение указано, оно будет включено в ответное сообщении в качестве способа сопоставления запроса. <p><p>**Важно!**<p>Значения должны превышать 2096 Б (2 КБ). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Стандартные ответы HTTP-запроса, поддерживаемые службами мультимедиа
Ниже приведен набор заголовков, которые могут быть возвращены в зависимости от запрошенного ресурса и действия, которое необходимо выполнить.

| Заголовок | type | Значение |
| --- | --- | --- |
| request-id |Строка |Уникальный идентификатор текущей операции, создаваемый службой. |
| client-request-id |Строка |Идентификатор, указанный вызывающим объектом в исходном запросе, если он имеется. |
| Дата |RFC 1123 date |Дата и время обработки запроса. |
| Content-Type |Varies |Тип содержимого текста ответа. |
| Content-Encoding |Varies |Соответственно, Gzip или Deflate. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Стандартные HTTP-команды, поддерживаемые службами мультимедиа
Ниже приведен полный список HTTP-команд, которые могут использоваться при создании HTTP-запросов:

| Команда | ОПИСАНИЕ |
| --- | --- |
| ПОЛУЧЕНИЕ |Возвращает текущее значение объекта. |
| ПУБЛИКАЦИЯ |Создает объект на основе предоставленных данных или отправляет команду. |
| ОТПРАВКА |Заменяет объект или создает именованный объект (в зависимости от ситуации). |
| УДАЛИТЬ |Удаляет объект. |
| MERGE |Вносит в существующий объект изменения в именованном свойстве. |
| HEAD |Возвращает метаданные объекта для ответа GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Обнаружение и просмотреть модель сущностей служб мультимедиа
Чтобы упростить обнаружение сущностей служб мультимедиа, можно использовать операцию $metadata. Она позволяет получать все допустимые типы сущностей, свойства сущностей, ассоциации, функции, действия и т. д. Добавив операцию $metadata конец конечной точке API REST служб мультимедиа, можно получить доступ к этой службы обнаружения.

 метаданные /API/$.

Чтобы метаданные можно было просмотреть в браузере, в конце универсального кода ресурса необходимо добавить выражение "?api-version=2.x" или не включать в запрос заголовок x-ms-version.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Проверка подлинности с REST служб мультимедиа с помощью Azure Active Directory

Проверка подлинности на API-интерфейса REST осуществляется посредством Azure Active Directory(AAD).
Дополнительные сведения о том, как получить сведения о требования проверки подлинности учетную запись служб мультимедиа с портала Azure см. в разделе [доступ к API служб мультимедиа Azure с проверкой подлинности Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Дополнительные сведения о написании кода, который подключается к API REST, с помощью проверки подлинности Azure AD, см. статью [проверки подлинности используют Azure AD для доступа к API служб мультимедиа Azure с ОСТАЛЬНОЙ](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Дальнейшие действия
Сведения об использовании проверки подлинности Azure AD с помощью API REST служб мультимедиа, в разделе [проверки подлинности используют Azure AD для доступа к API служб мультимедиа Azure с ОСТАЛЬНОЙ](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


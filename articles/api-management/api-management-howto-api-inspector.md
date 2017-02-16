---
title: "Как использовать инспектор API для трассировки вызовов в Azure API Management"
description: "Сведения о трассировке вызовов с помощью инспектора API в службе управления API Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 28e9cbf05e75055a10c78fd42b5cb27e359b1d65
ms.openlocfilehash: aad0e6de85a40df8a873aa11e5a33e7064090dce


---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Как использовать инспектор API для трассировки вызовов в Azure API Management
Служба управления содержит инспектор API, которое позволяет выполнять отладку и устранять неполадки интерфейсов API. Инспектор API может использоваться на программном уровне, а также напрямую из портала разработчика. 

Помимо операций трассировки, инспектор API также отслеживает оценку [выражений политики](https://msdn.microsoft.com/library/azure/dn910913.aspx). Демонстрацию см. в видеоролике [Cloud Cover, эпизод 177: возможности управления API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (перемотайте до 21:00).

Это руководство содержит пошаговые инструкции по использованию инспектора API.

> [!NOTE]
> Трассировка инспектором API доступна только для запросов, содержащих ключи подписки, принадлежащие учетной записи [администратора](api-management-howto-create-groups.md) .
> 
> 

## <a name="trace-call"> </a> Использование инспектора API для трассировки вызова
Для использования инспектора API добавьте заголовок запроса **ocp-apim-trace: true** в вызов операции, а затем скачайте и проверьте данные трассировки с помощью URL-адреса, указанного в заголовке ответа **ocp-apim-trace-location**. Это можно сделать на программном уровне или прямо на портале разработчика.

В этом руководстве объясняется, как с помощью инспектора API отслеживать операции, используя API "Базовый калькулятор", настроенный с помощью руководства [Начало работы со службой управления Azure API](api-management-get-started.md). Если вы еще не ознакомились с этим учебником, вам понадобится всего несколько секунд, чтобы импортировать базовый API калькулятора. Кроме того, можно использовать другой API по своему выбору, например Echo API. Каждый экземпляр службы API Management поставляется предварительно настроенным с Echo API, с которым можно экспериментировать при изучении API Management. Echo API возвращает всю отправленную в него информацию. Для его использования можно вызвать любую команду HTTP, и возвращаемое значение будет просто содержать все отправленные данные. 

Чтобы приступить к работе, на портале Azure щелкните **Портал разработчика** для службы управления API. Операции можно вызывать напрямую из портала разработчика, в который встроен удобный способ просмотра и проверки операций API.

> Если вы еще не создали экземпляр службы управления API, см. раздел [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Get started with Azure API Management].
> 
> 

![Портал разработчика API Management][api-management-developer-portal-menu]

Щелкните **API** в верхнем меню, а затем выберите **Базовый калькулятор**.

![Echo API][api-management-api]

Чтобы вызвать операцию **Сложение двух целых**, нажмите кнопку **Попробовать**.

![Попробовать][api-management-open-console]

Оставьте значения параметров по умолчанию и выберите ключ подписки для продукта, который необходимо использовать, в раскрывающемся списке **Ключ подписки** .

По умолчанию заголовок **Ocp-Apim-Trace** на портале разработчика уже имеет значение **true**. Этот заголовок указывает, должны ли создаваться трассировки.

![Отправить][api-management-http-get]

Нажмите кнопку **Отправить** , чтобы выполнить операцию.

![Отправить][api-management-send-results]

Заголовки ответа будут содержать **ocp-apim-trace-location** со значением, подобным на значение в следующем примере.

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

Данные трассировки можно скачать из указанного местоположения и проанализировать, как показано на следующем этапе. Обратите внимание, что сохраняются только последние 100 записей журнала и расположения журналов повторно используются в порядке очереди. Поэтому если сделано более 100 вызовов с включенной трассировкой, то в конечном счете начнут перезаписываться данные первых трассировок.

## <a name="inspect-trace"> </a>Проверка данных трассировки
Для анализа значений трассировки загрузите файл с результатами трассировки, используя URL-адрес **ocp-apim-trace-location** . Это текстовый файл в формате JSON, который содержит записи, подобные на записи в следующем примере.

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>Дальнейшие действия
* Посмотрите видеоруководство по выражениям политики трассировки в ролике [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Перемотайте демонстрацию до 21:00.

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png







<!--HONumber=Jan17_HO2-->



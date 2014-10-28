<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Как использовать инспектор API для трассировки вызовов в Azure API Management

API Management (предварительная версия) содержит инспектор API, которое позволяет выполнять отладку и устранять неполадки интерфейсов API. Инспектор API может использоваться на программном уровне из ваших приложений, а также прямо из портала разработчика. Это руководство содержит пошаговые инструкции по использованию инспектора API.

## Содержание раздела

-   [Использование инспектора API для трассировки вызова][Использование инспектора API для трассировки вызова]
-   [Проверка данных трассировки][Проверка данных трассировки]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="trace-call"> </a> Использование инспектора API для трассировки вызова

Для использования инспектора API добавьте заголовок запроса **ocp-apim-trace: true** в свой вызов операции, а затем загрузите и проверьте данные трассировки с помощью URL-адреса, указанного в заголовке ответа **ocp-apim-trace-location**. Это можно сделать на программном уровне или прямо на портале разработчика.

В данном учебнике показано, как использовать инспектор API для трассировки операций с помощью Echo API.

> Каждый экземпляр службы API Management поставляется предварительно настроенным с Echo API, с которым можно экспериментировать при изучении API Management. Echo API возвращает всю отправленную в него информацию. Для его использования можно вызвать любую команду HTTP, и возвращаемое значение будет просто содержать все отправленные данные.

Для начала работы щелкните **Портал разработки** на портале Azure для службы API Management. Открывается административный портал API Management.

> Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

![Портал разработчика API Management][Портал разработчика API Management]

Операции могут быть вызваны из портала разработчика, где предоставляется удобный способ просмотра и проверки операций API. На этом этапе вы вызовете метод **Get Resource** интерфейса **Echo API**.

Щелкните **API** в верхнем меню, а затем **Echo API**.

![Echo API][Echo API]

> Если есть только один настроенный или видимый API для данной учетной записи, тогда щелчок по API сразу приведет к операциям для этого API.

Выберите операцию **GET Resource** и щелкните **Открыть консоль**.

![Открытие консоли][Открытие консоли]

Оставьте значения параметров по умолчанию и выберите свой ключ подписки в раскрывающемся списке **Ключ подписки**.

Введите **ocp-apim-trace: true** в текстовом поле **Заголовки запроса** и щелкните **HTTP Get**.

![HTTP Get][HTTP Get]

Заголовки ответа будут содержать **ocp-apim-trace-location** со значением, подобным на значение в следующем примере.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Данные трассировки можно загрузить из указанного местоположения и проанализировать, как показано на следующем этапе.

## <a name="inspect-trace"> </a>Проверка данных трассировки

Для анализа значений трассировки загрузите файл с результатами трассировки, используя URL-адрес **ocp-apim-trace-location**. Это текстовый файл в формате JSON, который содержит записи, подобные на записи в следующем примере.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Дальнейшие действия

-   Просмотрите другие разделы в руководстве [Приступая к работе с расширенными параметрами API][Приступая к работе с расширенными параметрами API].

  [Использование инспектора API для трассировки вызова]: #trace-call
  [Проверка данных трассировки]: #inspect-trace
  [Дальнейшие действия]: #next-steps
  [Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
  [Начинаем работу с API Management]: ../api-management-get-started
  [Портал разработчика API Management]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Открытие консоли]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Приступая к работе с расширенными параметрами API]: ../api-management-get-started-advanced

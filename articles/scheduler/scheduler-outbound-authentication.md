---
title: Исходящая проверка подлинности планировщика
description: Исходящая проверка подлинности планировщика
services: scheduler
documentationcenter: .NET
author: krisragh
manager: dwrede
editor: ''

ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: krisragh

---
# Исходящая проверка подлинности планировщика
Задания планировщика могут вызывать службы, требующие проверки подлинности. В этом случае вызванная служба определяет, может ли задание планировщика получить доступ к ее ресурсам. В число таких служб входят другие службы Azure, Salesforce.com, Facebook и защищенные пользовательские веб-сайты.

## Добавление и удаление проверки подлинности
Добавить в задание планировщика проверку подлинности очень просто: при создании или обновлении задания добавьте в элемент `request` дочерний элемент JSON `authentication`. Секретные данные, передаваемые службе планировщика запросами PUT, POST или PATCH в составе объекта `authentication`, в ответах не отображаются. В ответах секретная информация обнуляется или получает открытый маркер, который представляет сущность, прошедшую проверку подлинности.

Чтобы удалить проверку подлинности, примените непосредственно к заданию запрос PUT или PATCH, установив для объекта `authentication` нулевое значение. Никакие свойства проверки подлинности в ответе показаны не будут.

В настоящее время поддерживаются только следующие типы проверки подлинности: модель `ClientCertificate` (для использования клиентских SSL/TLS-сертификатов), модель `Basic` (для обычной проверки подлинности) и модель `ActiveDirectoryOAuth` (для проверки подлинности Active Directory OAuth.)

## Текст запроса для проверки подлинности ClientCertificate
При добавлении проверки подлинности с использованием модели `ClientCertificate` укажите в тексте запроса следующие дополнительные элементы.

| Элемент | Описание |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования клиентского SSL-сертификата. |
| *type* |обязательный параметр. Тип проверки подлинности. Для клиентских SSL-сертификатов используйте значение `ClientCertificate`. |
| *pfx* |обязательный параметр. Содержимое PFX-файла с кодировкой Base64. |
| *password* |обязательный параметр. Пароль для доступа к PFX-файлу. |

## Текст ответа при проверке подлинности ClientCertificate
При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

| Элемент | Описание |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования клиентского SSL-сертификата. |
| *type* |Тип проверки подлинности. Для клиентских SSL-сертификатов это значение равно `ClientCertificate`. |
| *certificateThumbprint* |Отпечаток сертификата. |
| *certificateSubjectName* |Различающееся имя субъекта сертификата. |
| *certificateExpiration* |Срок действия сертификата. |

## Пример запроса REST для аутентификации ClientCertificate
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Пример ответа REST для аутентификации ClientCertificate
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## Текст запроса для обычной проверки подлинности
При добавлении проверки подлинности с использованием модели `Basic` укажите в тексте запроса следующие дополнительные элементы.

| Элемент | Описание |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования обычной проверки подлинности. |
| *type* |обязательный параметр. Тип проверки подлинности. Для обычной проверки подлинности используйте значение `Basic`. |
| *username* |обязательный параметр. Имя пользователя для проверки подлинности. |
| *password* |обязательный параметр. Пароль для проверки подлинности. |

## Текст ответа при обычной проверке подлинности
При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

| Элемент | Description (Описание) |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования обычной проверки подлинности. |
| *type* |Тип проверки подлинности. Для обычной проверки подлинности это значение равно `Basic`. |
| *username* |Имя пользователя, прошедшего проверку подлинности. |

## Пример запроса REST для обычной проверки подлинности
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Пример ответа REST для обычной проверки подлинности
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## Текст запроса для проверки подлинности ActiveDirectoryOAuth
При добавлении проверки подлинности с использованием модели `ActiveDirectoryOAuth` укажите в тексте запроса следующие дополнительные элементы.

| Элемент | Description (Описание) |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования проверки подлинности ActiveDirectoryOAuth. |
| *type* |обязательный параметр. Тип проверки подлинности. Для проверки подлинности ActiveDirectoryOAuth используйте значение `ActiveDirectoryOAuth`. |
| *tenant* |обязательный параметр. Идентификатор клиента Azure AD. |
| *audience* |обязательный параметр. Это свойство имеет значение https://management.core.windows.net/. |
| *clientid* |обязательный параметр. Укажите идентификатор клиента для приложения Azure AD. |
| *secret* |обязательный параметр. Секретные данные клиента, запрашивающего маркер. |

### Определение идентификатора клиента
Идентификатор клиента Azure AD можно узнать, выполнив команду `Get-AzureAccount` в Azure PowerShell.

## Текст ответа при проверке подлинности ActiveDirectoryOAuth
При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

| Элемент | Описание |
|:--- |:--- |
| *authentication (родительский элемент)* |Объект проверки подлинности для использования проверки подлинности ActiveDirectoryOAuth. |
| *type* |Тип проверки подлинности. Для аутентификации ActiveDirectoryOAuth это значение равно `ActiveDirectoryOAuth`. |
| *tenant* |Идентификатор клиента Azure AD. |
| *audience* |Имеет значение https://management.core.windows.net/. |
| *clientid* |Идентификатор клиента для приложения Azure AD. |

## Пример запроса REST для аутентификации ActiveDirectoryOAuth
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## Пример ответа REST для аутентификации ActiveDirectoryOAuth
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## См. также
 [Что такое планировщик?](scheduler-intro.md)

 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0817_2016-->
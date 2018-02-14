---
title: "Создание циклов и областей или индивидуальная обработка рабочих процессов в Azure Logic Apps | Документация Майкрософт"
description: "Узнайте о создании циклов для выполнения итерации по данным, группировании действий в области и индивидуальной обработке данных для запуска дополнительных рабочих процессов в Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 64b8f414efe8cd886589084f05e04486c9a0d05c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Циклы, области действия и индивидуальная обработка приложений логики
  
Среда Logic Apps предоставляет несколько способов работы с массивами, коллекциями, пакетами и циклами в рабочем процессе.
  
## <a name="foreach-loop-and-arrays"></a>Цикл и массивы ForEach
  
Приложения логики позволяют запускать циклы с использованием набора данных и выполнять определенное действие с каждым из них.  Запустить цикл с использованием коллекции можно с помощью действия `foreach`.  В конструкторе вы можете добавить цикл foreach.  Выбрав массив для перебора, можно приступать к добавлению действий.  Вы можете добавить несколько действий для каждого цикла foreach.  В цикле можно однократно указать, что должно происходить с каждым значением в массиве.

  В этом примере цикла отправляется сообщение на каждый адрес электронной почты, который содержит фрагмент microsoft.com. В представлении кода цикл foreach можно указать таким же образом, как в следующем примере:

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Действие `foreach` может выполнять итерацию массивов, содержащих тысячи сущностей.  Итерации выполняются параллельно по умолчанию.  Ограничения массивов и параллелизма см. в статье [Ограничения и настройка Logic Apps](logic-apps-limits-and-config.md).

### <a name="sequential-foreach-loops"></a>Последовательные циклы ForEach

Чтобы цикл ForEach мог выполняться последовательно, необходимо добавить параметр операции `Sequential`.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Цикл Until
  
  Действие или серию действий можно выполнять, пока не будет выполнено определенное условие.  Чаще всего цикл until применяется для вызова конечной точки до получения искомого ответа.  В конструкторе можно добавить цикл until.  После добавления действия в цикл можно задать условия выхода, а также ограничения для цикла.
  
  В этом примере конечная точка HTTP вызывается до тех пор, пока не будет получено значение "Completed" в тексте ответа.  Цикл завершается, если выполнено одно из следующих условий: 
  
  * HTTP-ответ имеет состояние "Выполнен".
  * Действие выполнялось один час.
  * Действие было выполнено 100 раз.
  
  В представлении кода цикл until можно указать так, как в следующем примере:
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn и индивидуальная обработка

Иногда триггер может получить массив элементов, которые нужно обработать индивидуально и запустить для каждого собственный рабочий процесс.  Эту индивидуальную обработку можно выполнить с помощью команды `spliton`.  Если триггер Swagger определяет полезные данные, составляющие массив, по умолчанию добавляется команда `spliton`. Команда `spliton` запускает выполнение каждого элемента в массиве.  Вы можете добавить SplitOn только к триггеру, который можно настроить или переопределить вручную. Одновременно использовать `spliton` и реализовать шаблон синхронных ответов невозможно.  Любой вызываемый рабочий процесс, содержащий действие `response` помимо `spliton`, выполняется асинхронно и отправляет незамедлительный ответ `202 Accepted`.  

  В этом примере показано получение массива элементов и их индивидуальная обработка в каждой строке. SplitOn можно указать так, как в следующем примере:

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Области действия

Последовательности действий можно группировать, используя область действий.  Области удобно использовать, когда реализуется обработка исключений.  Добавьте новую область в конструкторе и начните добавлять в нее действия.  Области можно определять в представлении кода, как в следующем примере:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

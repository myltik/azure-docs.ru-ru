---
title: "Определение рабочих процессов с помощью JSON. Azure Logic Apps | Документация Майкрософт"
description: "Запись определений рабочих процессов в JSON для приложений логики"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: e94837bf79e42602e2f72cda747ea629eed45a20
ms.openlocfilehash: 920940d8ebe23d24216d3e886bd8ae58be12ce34
ms.lasthandoff: 03/01/2017


---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Создание определений рабочих процессов для приложений логики с помощью JSON

Вы можете создать определения рабочих процессов для [Azure Logic Apps](logic-apps-what-are-logic-apps.md), используя простой декларативный язык JSON. Сначала ознакомьтесь со статьей [Создание нового приложения логики, подключающего службы SaaS](logic-apps-create-a-logic-app.md), если вы этого еще не сделали. Вы также можете просмотреть [полные справочные материалы по языку определения рабочего процесса](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Повторение шагов по списку

Для перебора массива, состоящего из 10 000 элементов, и выполнения действий для каждого из них можно использовать [тип foreach](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Обработка ошибок при сбоях

Как правило, вам требуется включить *шаг по исправлению* — определенную логику, которая используется, *только если* один или несколько из вызовов не удалось выполнить. В этом примере мы получаем данные из различных мест, но если вызов завершается неудачно, нам необходимо отправить куда-нибудь сообщение, чтобы можно было отследить этот сбой позже.  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Чтобы указать, что `postToErrorMessageQueue`, выполняющееся только после `readData`, находится в состоянии `Failed`, используйте свойство `runAfter`, например для указания списка возможных значений, чтобы `runAfter` могло перейти в состояние `["Succeeded", "Failed"]`.

Наконец, так как пример теперь обработал сбой, мы больше не помечаем выполнение меткой `Failed`. Так как в данный пример добавлен шаг для обработки этой ошибки, выполнение находится в состоянии `Succeeded`, хотя один шаг и не выполнился (`Failed`).

## <a name="execute-two-or-more-steps-in-parallel"></a>Параллельное выполнение двух (или более) шагов

Чтобы несколько действий выполнялись параллельно, свойство `runAfter` не должно изменяться во время выполнения. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

В этом примере для ветвей `branch1` и `branch2` задано выполнение после `readData`. В результате обе ветви будут выполняться параллельно. Метки времени для обеих ветвей идентичны.

![Параллельно](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Соединение двух параллельных ветвей

Два действия, настроенные для параллельного выполнения, можно объединить, добавив элементы в свойство `runAfter`, как показано в предыдущем примере.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Параллельно](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Сопоставление элементов списка с другой конфигурацией

Далее предположим, что мы хотим получать другое содержимое на основе значения свойства. Можно создать сопоставление значений и назначений как параметр.  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

В этом случае сначала мы получаем список статей. На втором шаге в сопоставлении мы выполняем поиск URL-адреса по категории, определенной как параметр, для получения содержимого.

Иногда следует отметить следующее: 

*    Функция [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) проверяет, соответствует ли категория одной из известных определенных категорий.

*    Получив категорию, мы можем извлечь элемент из сопоставления, используя квадратные скобки: `parameters[...]`.

## <a name="process-strings"></a>Строки для обработки

Для работы со строками можно использовать различные функции. Например, предположим, что у вас есть строка, которую необходимо передать в систему, но вы не уверены, что кодировка символов обработана правильно. Один из вариантов — закодировать эту строку в Base64. Однако во избежание экранирования в URL-адресе мы заменим несколько знаков. 

Нам также нужна подстрока из названия заказа, так как первые пять знаков не используются.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Для работы как внутри, так и снаружи сделайте следующее:

1. Получите [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) для названия заказа, и будет возвращено общее количество знаков.

2. Вычтите 5, так как нам понадобится более короткая строка.

3. Фактически возьмите [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Мы начнем с индекса `5` и перейдем к оставшейся части строки.

4. Преобразуйте эту подстроку в строку [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) все знаки `+` на `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) все знаки `/` на `_`.

## <a name="work-with-date-times"></a>Работа с датами и временем

Использовать даты и время может быть удобно, особенно в том случае, если вы пытаетесь извлечь данные из источника данных, который не поддерживает *триггеры* естественным образом. Вы также можете использовать даты и время, чтобы узнать продолжительность различных шагов.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

В этом примере производится извлечение `startTime` из предыдущего шага. Затем мы получим текущее время и вычтем одну секунду:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Вы можете использовать другие единицы времени, например `minutes` или `hours`. Наконец, мы можем сравнить эти два значения. Если первое значение меньше второго, то это значит, что с момента первоначального размещения заказа прошло больше секунды.

Для форматирования дат можно использовать модули форматирования строк. Например, чтобы получить RFC1123, мы используем [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Дополнительные сведения о форматировании даты см. в статье [Workflow Definition Language](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) (Язык определения рабочего процесса).

## <a name="deployment-parameters-for-different-environments"></a>Параметры развертывания для разных сред

Нередко жизненные циклы развертывания включают в себя среду разработки, промежуточную среду и рабочую среду. Например, в них во всех может использоваться одно и то же определение, но разные базы данных. Аналогично вы можете использовать одно определение в разных регионах для обеспечения высокого уровня доступности, но хотите, чтобы каждый экземпляр приложения логики взаимодействовал с базой данных своего региона.
Этот сценарий отличается от приема параметров во время *выполнения* тем, что вместо него следует использовать функцию `trigger()`, как показано в предыдущем примере.

Вы можете начать с базового определения, например:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

В фактическом запросе `PUT` для приложений логики можно указать параметр `uri`. Так как значение по умолчанию больше не существует, для полезных данных приложения логики требуется этот параметр:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

В каждой среде можно предоставить другое значение для параметра `connection`. 

В [документации по интерфейсу API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) описаны все параметры для создания приложений логики и управления ими. 


<properties 
	pageTitle="Создание определений приложений логики | Microsoft Azure" 
	description="Узнайте, как создать определение JSON для приложений логики." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="stepsic"/>
	
# Создание определений приложений логики
В этом разделе показано, как использовать определения [приложений логики службы приложений](app-service-logic-what-are-logic-apps.md), которые представляют собой простой декларативный язык JSON. Если вы этого еще не сделали, сначала ознакомьтесь с тем, [как создать новое приложение логики](../app-service-create-a-logic-app.md). Можно также прочитать [полные справочные материалы по языку определения на сайте MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx).

## Несколько действий по списку, которые повторяются

Общая схема такова: определить один шаг, который возвращает список элементов, а затем следовать последовательности из двух или более действий, которые вы хотите сделать по списку.

![Повторение по спискам](./media/app-service-logic-author-definitions/repeatoverlists.png)

В этом примере 3 действия.

1. Получение списка статей. При этом возвращается объект, содержащий массив.

2. Действие, которое переходит к свойству ссылки для каждой статьи и возвращает фактическое расположение статьи.

3. Действие, которое выполняет итерацию всех результатов из второго действия для скачивания фактических статей.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

Как указано в описании [использования функций приложений логики](app-service-logic-use-logic-app-features.md), итерация первого списка выполняется с помощью свойства `repeat:` из второго действия. Тем не менее для третьего действия необходимо выбрать свойство `@actions('readLinks').outputs.repeatItems`, так как второе выполняется для каждой статьи.

Внутри действия можно использовать функцию [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) или [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody). В этом примере мне было необходимо получить заголовок `location`, поэтому мной использована функция [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) для получения выходных данных выполнения второго действия, итерацию которых мы теперь осуществляем.

## Сопоставление элементов в списке с какой-либо другой конфигурацией

Далее предположим, что мы хотим получать совсем другое содержимое в зависимости от значения свойства. Можно создать сопоставление значений и назначений как параметр.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/ru-RU/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
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
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

В этом случае сначала мы получаем список статей, затем на втором шаге в сопоставлении выполняем поиск по категории, которая была определена как параметр, URL-адреса для получения содержимого.

Здесь следует уделить внимание двум моментам. Во-первых, это функция [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection), используемая для проверки, соответствует ли категория одной из известных определенных категорий. Во-вторых, когда мы получаем категорию, мы можем извлечь элемент из сопоставления с помощью квадратных скобок: `parameters[...]`.

## Добавление в цепочку и вложение приложений логики во время повторения для списка

Часто бывает проще управлять приложениями логики, когда они больше разделены. Это можно сделать, разделив логику на несколько определений и вызывая их из одного родительского определения. В этом примере будет родительское приложение логики, получающее заказы, и дочернее приложение логики, которое выполняет некоторые действия для каждого заказа.

В родительском приложении логики:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

Затем в дочернем приложении логики вы используете функцию [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) для получения значений, которые были переданы в дочерний рабочий процесс. Затем в качестве выходных данных укажите данные, которые необходимо вернуть в родительский поток.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

Вы можете прочитать о [действии типа приложения логики на сайте MSDN](https://msdn.microsoft.com/library/azure/dn948511.aspx).

>[AZURE.NOTE]Конструктор приложений логики не поддерживает действия типа приложения логики, поэтому вам потребуется вручную изменить определение.


## Шаг обработки ошибок, если что-то пошло не так

Обычно необходима возможность записать *шаги по исправлению* — определенную логику, которая используется, **только если** один или несколько из вызовов не удалось выполнить. В этом примере мы получаем данные из различных мест, но если вызов завершается неудачно, я хочу отправить (POST) сообщение куда-то, чтобы можно было отследить этот сбой позже.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ru-RU/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

Я использую два условия, так как на первом шаге выполняется повторение для списка. Если имеется только одно действие, вам потребуется только одно условие (первое). Также обратите внимание, что можно использовать *входы* в действие, завершившееся сбоем, на шаге исправления — здесь я передаю вызвавший сбой URL-адрес на второй шаг.

![Исправление](./media/app-service-logic-author-definitions/remediation.png)

Наконец, так как сбой теперь обработан, мы больше не помечаем выполнение меткой **Сбой**. Как видите, это выполнение **успешно** завершено, хотя на одном шаге произошел сбой, так как мной был написан шаг для обработки данного сбоя.

## Два (или более) шага, выполняемые параллельно

Для выполнения нескольких действий одновременно, а не последовательно необходимо удалить условие `dependsOn`, которое связывает эти два действия. После удаления зависимости действия автоматически будут выполняться параллельно, если только им не требуются данные друг от друга.

![Ветви](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ru-RU/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

Как видно в приведенном выше примере, branch1 и branch2 зависят только от содержимого из readData. В результате обе эти ветви будут выполняться одновременно.

![Параллельно](./media/app-service-logic-author-definitions/parallel.png)

Как видите, метки времени для обеих ветвей идентичны.

## Соединение двух условных ветвей логики

Можно объединить два условных потока логики (которые могут быть выполнены или нет), задав одно действие, которое использует данные из обеих ветвей.

Ваша стратегия при этом зависит от того, обрабатываете вы один элемент или коллекцию элементов. Если используется один элемент, вам нужно будет задействовать функцию [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce).

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
Кроме того, если первые две ветви работают, например, со списком заказов, то, возможно, необходимо будет использовать функцию [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) для объединения данных из обеих ветвей.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## Работа со строками

Существуют различные функции, которые могут оперировать строкой. Рассмотрим пример, когда у нас есть строка, которую необходимо передать в систему, но вы не уверены, что кодировка символов будет обработана правильно. Один из вариантов — закодировать эту строку в Base64. Однако во избежание экранирования в URL-адресе мы заменим несколько знаков.

Нам также нужна подстрока из названия заказа, так как первые пять знаков не используются.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
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

Работа "наизнанку"

1. Получите [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) названия заказа, и будет возвращено общее число знаков.

2. Вычтите 5 (так как нам понадобится более короткая строка).

3. Фактически возьмите [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring). Мы начнем с индекса `5` и перейдем к оставшейся части строки.

4. Преобразуйте эту подстроку в строку [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) все знаки `+` на `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) все знаки `/` на `_`.

## Работа с датами и временем

Использовать даты и время может быть удобно, особенно в том случае, если вы пытаетесь извлечь данные из источника данных, который не поддерживает **триггеры** естественным образом. Также можно использовать даты и время, чтобы узнать продолжительность различных шагов.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

В этом примере производится извлечение `startTime` из предыдущего шага. Затем мы получаем текущее время и вычитаем одну секунду: [`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (можно использовать и другие единицы времени, например `minutes` или `hours`). Наконец, мы можем сравнить эти два значения. Если первое меньше второго, то это значит, что с момента первоначального размещения заказа прошло больше секунды.

Также обратите внимание, что можно использовать модули форматирования строк для форматирования дат: в строке запроса я использую [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow), чтобы получить RFC1123. Все сведения о форматировании дат [приведены на сайте MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Передача значений во время выполнения для изменения поведения

Предположим, имеются различные поведения, которые необходимо выполнять по некоторому значению, используемому для запуска приложения логики. Можно использовать функцию [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs), чтобы получить эти значения на основании переданных данных.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

Чтобы это действительно заработало, при запуске выполнения необходимо передать нужные свойства (в приведенном выше примере `uriToGet` и `doMoreLogic`). Вот вызов, для которого вы можете [использовать обычную проверку подлинности](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth).

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Со следующими полезными данными. Обратите внимание, что теперь вы предоставили приложению логики значения для использования.

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

При запуске это приложение логики вызывает переданный мной универсальный код ресурса (URI) и выполняет этот дополнительный шаг, так как мной передано значение `true`. Если изменять параметры необходимо только во время развертывания (не при *каждом запуске*), то следует использовать `parameters`, которые вызываются ниже.

## Использование параметров во время развертывания для разных сред

Нередко жизненный цикл развертывания включает в себя среду разработки, промежуточную среду и рабочую среду. Во всех них может использоваться одно и то же определение, например, но разные базы данных. Аналогично вы можете использовать одно определение в разных регионах для обеспечения высокого уровня доступности, но хотите, чтобы каждый экземпляр приложения логики взаимодействовал с базой данных своего региона.

Обратите внимание, что это отличается от приема разных параметров во *время выполнения*, когда следует использовать функцию `trigger()`, вызванную выше.

Можно начать с очень простого определения, например:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

Затем в фактическом запросе `PUT` для приложения логики можно указать параметр `connection`. Обратите внимание, что, так как значения по умолчанию больше нет, этот параметр является обязательным в полезных данных приложения логики.

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
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

Затем в каждой среде можно предоставить другое значение для параметра `connection`.

## Выполнение шага до тех пор, пока не будет выполнено условие

Представим, что есть интерфейс API, который нужно вызвать. При этом необходимо дождаться определенного ответа перед продолжением. Представим также, что требуется подождать, пока кто-то отправит файл в каталог перед обработкой файла. Это можно сделать с помощью *do-until*:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

В [документации по интерфейсу API REST](https://msdn.microsoft.com/library/azure/dn948513.aspx) описаны все параметры для создания приложений логики и управления ими.

<!---HONumber=AcomDC_1210_2015-->
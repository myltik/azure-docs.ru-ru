<properties 
	pageTitle="Создание определений приложений логики | Microsoft Azure" 
	description="Узнайте, как создать определение JSON для приложений логики." 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# Создание определений приложений логики
В этой статье показано, как использовать определения [Azure Logic Apps](app-service-logic-what-are-logic-apps.md), являющиеся простым декларативным языком JSON. Если вы этого еще не сделали, сначала ознакомьтесь с тем, [как создать новое приложение логики](app-service-logic-create-a-logic-app.md). Можно также прочитать [полные справочные материалы по языку определения на сайте MSDN](http://aka.ms/logicappsdocs).

## Несколько действий по списку, которые повторяются

Для массива, состоящего из 10 000 элементов, можно использовать [тип foreach](app-service-logic-loops-and-scopes.md) для повторения действий или выполнения действий для каждого из элементов.

## Шаг обработки ошибок, если что-то пошло не так

Обычно необходима возможность записать *шаги по исправлению* — определенную логику, которая используется, **только если** один или несколько из вызовов не удалось выполнить. В этом примере мы получаем данные из различных мест, но если вызов завершается неудачно, я хочу отправить (POST) сообщение куда-то, чтобы можно было отследить этот сбой позже.

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

Вы можете использовать свойство `runAfter`, чтобы настроить запуск `postToErrorMessageQueue` только после неудачного выполнения `readData` (**Failed**). Возможные значения свойства `runAfter` также могут быть представлены списком, например `["Succeeded", "Failed"]`.

Наконец, так как сбой теперь обработан, мы больше не помечаем выполнение меткой **Сбой**. Как видите, это выполнение **успешно** завершено, хотя на одном шаге произошел сбой, так как мной был написан шаг для обработки данного сбоя.

## Два (или более) шага, выполняемые параллельно

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

Как видно в приведенном выше примере, для ветвей `branch1` и `branch2` задано выполнение после `readData`. В результате обе эти ветви будут выполняться одновременно.

![Параллельно](./media/app-service-logic-author-definitions/parallel.png)

Как видите, метки времени для обеих ветвей идентичны.

## Соединение двух параллельных ветвей

Можно объединить два действия, которые были настроены для параллельного выполнения, добавив элементы в свойство `runAfter` (см. выше).

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

![Параллельно](./media/app-service-logic-author-definitions/join.png)

## Сопоставление элементов в списке с какой-либо другой конфигурацией

Далее предположим, что мы хотим получать совсем другое содержимое в зависимости от значения свойства. Можно создать сопоставление значений и назначений как параметр.

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
				"microsoft": "https://www.microsoft.com/ru-RU/default.aspx",
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

В этом случае сначала мы получаем список статей, затем на втором шаге в сопоставлении выполняем поиск по категории, которая была определена как параметр, URL-адреса для получения содержимого.

Здесь следует уделить внимание двум моментам. Во-первых, это функция [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection), используемая для проверки, соответствует ли категория одной из известных определенных категорий. Во-вторых, когда мы получаем категорию, мы можем извлечь элемент из сопоставления с помощью квадратных скобок: `parameters[...]`.

## Работа со строками

Существуют различные функции, которые могут оперировать строкой. Рассмотрим пример, когда у нас есть строка, которую необходимо передать в систему, но вы не уверены, что кодировка символов будет обработана правильно. Один из вариантов — закодировать эту строку в Base64. Однако во избежание экранирования в URL-адресе мы заменим несколько знаков.

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

Работа "наизнанку"

1. Получите [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) названия заказа, и будет возвращено общее число знаков.

2. Вычтите 5 (так как нам понадобится более короткая строка).

3. Фактически возьмите [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Мы начнем с индекса `5` и перейдем к оставшейся части строки.

4. Преобразуйте эту подстроку в строку [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) все знаки `+` на `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) все знаки `/` на `_`.

## Работа с датами и временем

Использовать даты и время может быть удобно, особенно в том случае, если вы пытаетесь извлечь данные из источника данных, который не поддерживает **триггеры** естественным образом. Также можно использовать даты и время, чтобы узнать продолжительность различных шагов.

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
		"manual": {
			"type": "manual"
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
		"timingWarning": {
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

В этом примере производится извлечение `startTime` из предыдущего шага. Затем мы получаем текущее время и вычитаем одну секунду: [`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (можно использовать и другие единицы времени, например `minutes` или `hours`). Наконец, мы можем сравнить эти два значения. Если первое меньше второго, то это значит, что с момента первоначального размещения заказа прошло больше секунды.

Также обратите внимание, что можно использовать модули форматирования строк для форматирования дат: в строке запроса я использую [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow), чтобы получить RFC1123. Все сведения о форматировании дат [приведены на сайте MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Использование параметров во время развертывания для разных сред

Нередко жизненный цикл развертывания включает в себя среду разработки, промежуточную среду и рабочую среду. Во всех них может использоваться одно и то же определение, например, но разные базы данных. Аналогично вы можете использовать одно определение в разных регионах для обеспечения высокого уровня доступности, но хотите, чтобы каждый экземпляр приложения логики взаимодействовал с базой данных своего региона.

Обратите внимание, что это отличается от приема разных параметров во *время выполнения*, когда следует использовать функцию `trigger()`, вызванную выше.

Можно начать с очень простого определения, например:

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

Затем в фактическом запросе `PUT` для приложения логики можно указать параметр `uri`. Обратите внимание, что, так как значения по умолчанию больше нет, этот параметр является обязательным в полезных данных приложения логики.

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

Затем в каждой среде можно предоставить другое значение для параметра `connection`.

В [документации по интерфейсу API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) описаны все параметры для создания приложений логики и управления ими.

<!---HONumber=AcomDC_0803_2016-->
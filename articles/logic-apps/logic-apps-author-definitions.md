---
title: Создание, редактирование и расширение JSON для определений в Azure Logic Apps | Документация Майкрософт
description: Создание и настройка определений в формате JSON для приложений логики
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 9793fdf2bd351bd1f15bcb88ffd25d6b19485303
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297858"
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Создание, редактирование и настройка JSON для определений в приложениях логики

При создании корпоративных решений по интеграции с автоматизированными рабочими процессами на основе [Azure Logic Apps](../logic-apps/logic-apps-overview.md) для описания и проверки определений используется простой декларативный язык JSON (JavaScript Object Notation) и [схема WDL (языка определения рабочего процесса)](../logic-apps/logic-apps-workflow-definition-language.md). Эти форматы позволяют легко читать и понимать определения приложений логики даже без глубоких знаний о программировании. Если вы хотите автоматизировать создание и развертывание приложений логики, включите определения приложений логики в [шаблоны Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) в качестве [ресурсов Azure](../azure-resource-manager/resource-group-overview.md). Тогда вы сможете создавать приложения логики, управлять ими и развертывать их с помощью [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) или [интерфейсов API REST для Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Вы можете открыть определения приложений логики в формате JSON, используя редактор представления кода на портале Azure или в Visual Studio, или любой другой текстовый редактор. Если вы пока не знакомы с приложениями логики, изучите статью [о создании первого приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Некоторые возможности Azure Logic Apps, например определение параметров и множественные триггеры, можно включить в определения только напрямую через JSON, но нельзя использовать в конструкторе Logic Apps. Если вам нужны эти функции, используйте представление кода или другой текстовый редактор.

## <a name="edit-json---azure-portal"></a>Редактирование JSON на портале Azure

1. Войдите на <a href="https://portal.azure.com" target="_blank">портале Azure</a>.

2. В меню слева выберите **Все службы**. В поле поиска введите строку "приложения логики" и выберите нужное приложение в результатах поиска.

3. В меню для этого приложения логики откройте раздел **Средства разработки** и выберите **Представление кода приложения логики**.

   Откроется окно представления кода, в котором вы увидите определения приложения логики в формате JSON.

## <a name="edit-json---visual-studio"></a>Редактирование JSON в Visual Studio

Прежде чем редактировать определения приложений логики в Visual Studio, следует [установить все необходимые средства](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Чтобы создать приложение логики в Visual Studio, воспользуйтесь [кратким руководством по автоматизации задач и процессов с помощью Azure Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Visual Studio позволяет открыть приложения логики, которые были созданы и развернуты напрямую через портал Azure или из Visual Studio в виде проектов Azure Resource Manager.

1. Откройте решение Visual Studio или проект [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md), который содержит нужное приложение логики.

2. Найдите и откройте определение приложения логики. По умолчанию они содержатся в [шаблоне диспетчера ресурсов](../azure-resource-manager/resource-group-overview.md#template-deployment) под именем **LogicApp.json**. Вы можете применять и корректировать этот шаблон для развертывания в разных средах.

3. Откройте контекстное меню для определения и шаблона приложения логики. Выберите действие **Открыть в конструкторе приложений логики**.

   ![Открытие приложения логики в решении Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. В нижней части конструктора выберите **Представление кода**. 

   Откроется окно представления кода, в котором вы увидите определения приложения логики в формате JSON.

5. Чтобы вернуться в представление конструктора, в нижней части редактора представления кода, выберите **Конструктор**.

## <a name="parameters"></a>Параметры

Параметры позволяют повторно использовать значения по всему приложению логики и хорошо подходят для замены значений, которые могут часто меняться. Например, если вы хотите использовать один адрес электронной почты в нескольких местах, можно задать его как параметр. 

Параметры также полезны, если их нужно переопределять в различных средах. Дополнительные сведения см. в разделе о [параметрах для развертывания](#deployment-parameters) и в [документации по REST API для Azure Logic Apps](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Параметры доступны только в представлении кода.

В [первом примере приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) вы создали рабочий процесс, который отправляет сообщения электронной почты при появлении новых записей на веб-канале RSS веб-сайта. URL-адрес веб-канала является жестко запрограммированным, поэтому в этом примере показано, как заменить значение запроса с помощью параметра, чтобы иметь возможность легко изменять URL-адрес веб-канала.

1. В представлении кода найдите объект `parameters : {}` и вставьте в него следующий объект `currentFeedUrl`:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. В действии `When_a_feed-item_is_published` найдите раздел `queries` и замените значение запроса значением `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **До:**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **После:**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Чтобы объединить несколько строк, можно использовать функцию `concat`. 
   Например, `"@concat('#',parameters('currentFeedUrl'))"` действует так же, как в предыдущем примере.

3.  Когда все будет готово, нажмите **Сохранить**. 

Теперь вы можете изменить RSS-канал веб-сайта, передав другой URL-адрес через объект `currentFeedURL`.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Параметры развертывания для разных сред

Обычно жизненные циклы развертывания имеют среды для развертывания, промежуточного хранения и рабочую среду. Например, во всех средах может использоваться одно и то же определение приложения логики, но разные базы данных. Аналогично вы можете использовать одно определение в разных регионах для обеспечения высокого уровня доступности, но хотите, чтобы каждый экземпляр приложения логики взаимодействовал с базой данных своего региона. 

> [!NOTE] 
> Этот сценарий отличается от приема параметров во время *выполнения* тем, что вместо него следует использовать функцию `trigger()`.

Вот основное определение:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
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
В фактическом запросе `PUT` для приложений логики можно указать параметр `uri`. В каждой среде можно предоставить другое значение для параметра `connection`. Так как значение по умолчанию больше не существует, для полезных данных приложения логики требуется этот параметр:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
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

Дополнительные сведения см. в [документации по REST API для Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Обработка строк с помощью функций

Logic Apps имеет различные функции для работы со строками. Например, предположим, что вам требуется передать имя компании из заказа в другую систему. Однако вы не уверены, как правильно обработать кодировку символов. К этой строке можно применить кодировку base64, однако чтобы избежать символов экранирования в URL-адресе, можно заменить несколько символов. Кроме того, вам нужна только подстрока для названия компании, так как первые пять символов не используются. 

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Ниже показано, как этот пример обрабатывает строку, работая "наизнанку":

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Запросите [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) для названия компании, и будет возвращено общее количество знаков.

2. Чтобы получить более короткую строку, вычтите `5`.

3. Вы получите [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Начнем с индекса `5` и перейдем к оставшейся части строки.

4. Преобразуйте эту подстроку в строку [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md).

5. Теперь замените ([`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)) все знаки `+` на `-`.

6. Наконец замените ([`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)) все знаки `/` на `_`.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Сопоставление элементов списка со значениями свойств с последующим использованием сопоставления в качестве параметра

Чтобы получить различные результаты на основе значения свойства, можно создать схему, сопоставляющую каждое значение свойства с результатом, а затем использовать сопоставление в качестве параметра. 

Например, этот рабочий процесс определяет некоторые категории как параметры и схему, которая сопоставляет категории с определенным URL-адресом. Сначала рабочий процесс получает список статей. Затем он использует схему для поиска соответствия URL-адреса и категории для каждой статьи.

*   Функция [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) проверяет, соответствует ли категория известной определенной категории.

*   После получения соответствующей категории пример извлекает элемент из схемы, используя квадратные скобки: `parameters[...]`.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Получение данных с помощью функций данных

Чтобы получить данные из источника данных, который не поддерживает *триггеры*, для работы со временем и датами можно использовать функции данных. Например, это выражение определяет, как долго выполняются шаги этого рабочего процесса, работая "наизнанку":

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Из действия `order` извлеките `startTime`. 
2. Получите текущее время с помощью `utcNow()`.
3. Вычтите одну секунду:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Вы можете использовать другие единицы времени, например `minutes` или `hours`. 

3. Теперь вы можете сравнить эти два значения. 

   Если первое значение меньше второго, то это значит, что с момента первоначального размещения заказа прошло больше секунды.

Для форматирования дат можно использовать модули форматирования строк. Например, чтобы получить RFC1123, используйте [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). См. дополнительные сведения о [форматировании даты](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
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


## <a name="next-steps"></a>Дополнительная информация

* [Conditional statements: Run steps based on a condition in logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Условные операторы. Выполнение шагов на основе условия в приложениях логики)
* [Операторы switch. Выполнение различных шагов на основе различных значений в приложениях логики](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Циклы. Обработка массивов или повторение действий до выполнения условия)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Создание или присоединение параллельных ветвей в приложении логики)
* [Области. Выполнение шагов на основе состояния группы в приложениях логики](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Схема языка определения рабочих процессов в Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* [Действия и триггеры рабочих процессов в Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
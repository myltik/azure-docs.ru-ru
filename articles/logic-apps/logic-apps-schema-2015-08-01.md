---
title: "Обновления схемы от&1; августа&2015; г. Azure Logic Apps | Документация Майкрософт"
description: "Создание определений JSON для Azure Logic Apps с версией схемы 2015-08-01-preview"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: a3ae5ce7d16ac9ed3a06c313ff3c48e8cbf79c54
ms.openlocfilehash: f1bf19e6dfbcf187635730a53c93162244c17c6a
ms.lasthandoff: 03/01/2017


---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Обновления схемы для Azure Logic Apps от 1 августа 2015 г. (ознакомительная версия)

Эта новая версия схемы и API для Azure Logic Apps включает основные улучшения, которые повышают надежность приложений логики и упрощают их использование.

*    Вместо типа действия **APIApp** введен новый тип действия [**APIConnection**](#api-connections).
*    Действие **Repeat** переименовано в [**Foreach**](#foreach).
*    [**Прослушиватель HTTP** (приложение API)](#http-listener) больше не требуется.
*    При вызове дочерних рабочих процессов используется [новая схема](#child-workflows).

<a name="api-connections"></a>
## <a name="move-to-api-connections"></a>Переход к API-подключениям

Самое значительное изменение в этом обновлении: для использования API больше не нужно развертывать приложения API в подписке Azure. Ниже приведены способы использования API.

* Управляемые API
* Пользовательские веб-API

Обработка этих способов немного отличается из-за разных моделей управления и размещения. Одно из преимуществ этой модели заключается в том, что вы больше не ограничены ресурсами, развернутыми в вашей группе ресурсов Azure. 

### <a name="managed-apis"></a>Управляемые API

Корпорация Майкрософт управляет некоторыми интерфейсами API от вашего имени, например Office 365, Salesforce, Twitter и FTP. Некоторые из управляемых интерфейсов API, например переводчик Bing Translate, можно использовать "как есть", а для других требуется настройка. Такая конфигурация называется *подключением*.

Например, при использовании Office 365 необходимо создать подключение, которое содержит токен входа в Office 365. Этот токен безопасно хранится и обновляется, поэтому ваше приложение логики всегда может вызвать интерфейс API Office 365. С другой стороны, для подключения к FTP-серверу или SQL Server необходимо создать подключение, которое содержит строку подключения. 

В определении эти действия называются `APIConnection`. Вот пример подключения, которое вызывает Office 365 для отправки сообщения электронной почты:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

Объект `host` — это часть входных данных, уникальная для подключений API, которая состоит из двух компонентов: `api` и `connection`.

`api` содержит URL-адрес среды выполнения, в которой размещается управляемый API-интерфейс. Все доступные управляемые интерфейсы API можно просмотреть путем вызова `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

При использовании интерфейса API в нем могут быть определены или не определены *параметры подключения*. Если они не определены, то *подключение* не требуется. Если они определены, необходимо создать подключение. Выберите имя для этого подключения. Затем необходимо сослаться на это имя в объекте `connection` внутри объекта `host`. Чтобы создать подключение в группе ресурсов, выполните следующий вызов:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Текст должен быть следующим:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Развертывание управляемых интерфейсов API в шаблоне Azure Resource Manager

В шаблоне Azure Resource Manager можно создать полное приложение, если для этого не требуется интерактивный вход.
Если вход требуется, то можно настроить все параметры в шаблоне Azure Resource Manager, но для авторизации подключений все равно придется посетить портал. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/', parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:, Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

В этом примере можно увидеть, что подключения — это просто ресурсы, находящиеся в группе ресурсов. Они ссылаются на управляемые интерфейсы API, доступные в подписке.

### <a name="your-custom-web-apis"></a>Пользовательские веб-API

Если вы используете собственные интерфейсы API (не управляемые корпорацией Майкрософт), используйте встроенное действие **HTTP** для их вызова. Чтобы обеспечить идеальную работу, для интерфейса API следует предоставлять конечную точку Swagger. Эта конечная точка позволяет конструктору приложений логики обрабатывать входные и выходные данные для API. Без конечной точки Swagger конструктор может отображать входные и выходные данные только как непрозрачные объекты JSON.

Вот пример, в котором показано новое свойство `metadata.apiDefinitionUrl` :

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Если веб-API размещается в службе приложений Azure, он автоматически отображается в списке действий, доступных в конструкторе. В противном случае необходимо вставить URL-адрес напрямую. Конечная точка Swagger должна быть непроверенной, чтобы ее можно было использовать в конструкторе приложений логики. Хотя вы можете защитить сам API с помощью любого метода, поддерживаемого в Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Вызов развернутых приложений API с версией 2015-08-01-preview

Если вы уже развернули приложение API, его можно вызвать с помощью действия **HTTP** .

Например, если вы используете Dropbox для просмотра списка файлов, то в определении схемы версии **2014-12-01-preview** может содержаться что-то подобное этому:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Вы можете создать такое же действие HTTP, как в этом примере. Раздел параметров в определении приложения логики остается без изменений.

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata": {
              "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method": "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Вот описаний каждого из этих свойств:

| Свойство действия | Описание |
| --- | --- |
| `type` |`Http` вместо `APIapp` |
| `metadata.apiDefinitionUrl` |Чтобы использовать это действие в конструкторе приложений логики, включите конечную точку метаданных, которая состоит из следующих компонентов: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Состоит из следующих компонентов: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Всегда `POST` |
| `inputs.body` |Идентично параметрам приложения API |
| `inputs.authentication` |Идентично проверке подлинности приложения API |

Этот подход должен работать для всех действий приложений API. Обратите внимание, эти предыдущие приложения API больше не поддерживаются. Поэтому вы должны перейти на один из других вариантов: управляемый интерфейс API или размещение настраиваемого веб-API.

<a name="foreach"></a>
## <a name="renamed-repeat-to-foreach"></a>Переименование действия Repeat на Foreach

За время использования предыдущей версии схемы мы получили множество отзывов клиентов о том, что название **Repeat** приводит к путанице и не отображает того, что действие **Repeat** применяется к каждому циклу. Поэтому мы переименовали `repeat` на `foreach`. Например, ранее вы бы написали так:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

А теперь следует писать так:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Ранее функция `@repeatItem()` использовалась для указания того, что с текущим элементом выполняется итерация. Теперь эта функция упрощена до `@item()`. 

### <a name="reference-outputs-from-foreach"></a>Эталонные выходные данные из Foreach

Для упрощения выходные данные действия `foreach` не упаковываются в объект с именем `repeatItems`. Выходные данные предыдущего действия `repeat` выглядели так:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Теперь они имеют такой вид:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Раньше, чтобы получить текст действия, ссылаясь на эти выходные данные, необходимо было сделать следующее:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "repeat": "@outputs('pingBing').repeatItems",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@repeatItem().outputs.body"
            }
        }
    }
}
```

Теперь вместо этого можно сделать следующее:

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "foreach": "@outputs('pingBing')",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@item().outputs.body"
            }
        }
    }
}
```

Эти изменения сопряжены с удалением функций `@repeatItem()`, `@repeatBody()` и `@repeatOutputs()`.

<a name="http-listener"></a>
## <a name="native-http-listener"></a>Встроенный прослушиватель HTTP

Функции прослушивателя HTTP теперь встроены. Поэтому больше не требуется развертывать приложение API прослушивателя HTTP. См. статью [Приложения логики как вызываемые конечные точки](../logic-apps/logic-apps-http-endpoint.md). 

После внесения этих изменений функция `@accessKeys()` была удалена и заменена функцией `@listCallbackURL()` для получения конечной точки при необходимости. Кроме того, теперь в приложении логики необходимо определить хотя бы один триггер. Если вы хотите запустить (`/run`) рабочий процесс, вам потребуется один из этих триггеров: `manual`, `apiConnectionWebhook` или `httpWebhook`.

<a name="child-workflows"></a>
## <a name="call-child-workflows"></a>Вызов дочерних рабочих процессов

Ранее для вызова дочерних рабочих процессов нужно было перейти к рабочему процессу, получить токен доступа и вставить его в определение приложения логики, которое должно вызвать этот дочерний рабочий процесс. В новой схеме ядро Logic Apps автоматически создает SAS в среде выполнения для дочернего рабочего процесса. Поэтому вам не нужно вставлять секреты в определение. Пример:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
        },
        "queries": {
            "extrafield": "specialValue"
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "conditions": []
}
```

Второе улучшение заключается в том, что дочерние процессы получают полный доступ к входящим запросам. Это означает, что вы можете передавать параметры в раздел *queries* и объект *headers*, а также полностью определять весь текст.

Наконец, внесены изменения, необходимые для дочернего рабочего процесса. Раньше дочерний рабочий процесс можно было вызвать напрямую. Теперь, чтобы родительский рабочий процесс мог выполнить вызов, необходимо определить конечную точку триггера в рабочем процессе. В общих чертах это означает, что вам необходимо добавить триггер типа `manual`, а затем использовать его в определении родительского рабочего процесса. Обратите внимание, что свойство `host` содержит элемент `triggerName`, так как всегда следует указывать вызываемый триггер.

## <a name="other-changes"></a>Другие изменения

### <a name="new-queries-property"></a>Новое свойство queries

Все типы действий теперь поддерживают новые входные данные, называемые `queries`. Эти входные данные могут быть структурированным объектом, который избавит вас от необходимости собирать строку вручную.

### <a name="renamed-parse-function-to-json"></a>Переименованные функции parse() на json

Скоро будут добавлены другие типы содержимого, поэтому мы изменили имя функции с `parse()` на `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Ожидается в ближайшее время: API-интерфейсы для интеграции Enterprise

У нас пока нет управляемых версий API интеграции Enterprise, например AS2. Пока вы можете использовать существующие развернутые API BizTalk, используя действие HTTP. Дополнительные сведения см. в разделе об использовании уже развернутых приложений API в [схеме интеграции](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 


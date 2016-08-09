<properties 
	pageTitle="Новая версия схемы 2015-08-01-preview" 
	description="Отсюда вы узнаете, как создать определение JSON для приложений логики последней версии." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="stepsic"/>
	
# Новая версия схемы 2015-08-01-preview

Новая версия схемы и API для приложений логики содержит ряд улучшений, которые повышают надежность и простоту использования приложений логики. Четыре основных отличия новой версии:

1. Вместо типа действия **APIApp** введен новый тип действия **APIConnection**.
2. Действие **Repeat** переименовано в **Foreach**.
3. Приложение API **прослушивателя HTTP** больше не требуется.
4. При вызове дочерних рабочих процессов используется новая схема.

## 1\. Переход к API-подключениям

Самым значительным изменением является то, что для использования API больше не нужно развертывать приложения API в подписке Azure. Существует два способа использования API-интерфейсов:
* Управляемые API-интерфейсы
* Пользовательские интерфейсы веб-API

Обработка этих способов немного отличается из-за разных моделей управления и размещения. Одно из преимуществ этой модели заключается в том, что вы больше не ограничены ресурсами, развернутыми в вашей группе ресурсов.

### Управляемые API

Существует ряд API-интерфейсов, которыми корпорация Майкрософт управляет от вашего имени, например Office 365, Salesforce, Twitter, FTP и т. д. Некоторые из этих управляемых API-интерфейсов, например переводчик Bing Translate, можно использовать "как есть", для других требуется настройка, Такая конфигурация называется *подключением*.

Например, при использовании Office 365 необходимо создать подключение, которое содержит маркер входа в Office 365. Этот маркер будет безопасно храниться и обновляться, поэтому ваше приложение логики всегда сможет вызвать интерфейс API Office 365. С другой стороны, для подключения к серверу FTP или SQL Server необходимо создать подключение, которое содержит строку подключения.

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

Часть входных данных, уникальная для API-подключений, — это объект `host`. Он состоит из двух компонентов: `api` и `connection`.

`api` содержит URL-адрес среды выполнения, в которой размещается управляемый API-интерфейс. Все доступные для вас управляемые API-интерфейсы можно просмотреть путем вызова `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

При использовании API-интерфейса в нем могут быть определены или не определены **параметры подключения**. Если они не определены, **подключение** не требуется. Если они определены, необходимо создать подключение. При создании подключения ему будет присвоено выбранное вами имя, а затем вы сможете ссылаться на него в объекте `connection` внутри объекта `host`. Чтобы создать подключение в группе ресурсов, выполните следующий вызов:

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
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Развертывание управляемых API-интерфейсов в шаблоне Azure Resource Manager

В шаблоне ARM можно создать полное приложение, если для этого не требуется интерактивный вход. Если вход требуется, можно настроить все параметры в шаблоне ARM, но для авторизации подключений все равно придется посетить портал.

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
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
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
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
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

В этом примере можно увидеть, что подключения — это обычные ресурсы, находящиеся в группе ресурсов. Они ссылаются на управляемые API-интерфейсы, доступные в подписке.

### Пользовательские веб-API

Если вы используете собственные API-интерфейсы (в частности, не управляемые корпорацией Майкрософт), для их вызова следует использовать встроенное действие **HTTP**. Чтобы обеспечить идеальную работу, для API-интерфейса следует предоставлять конечную точку Swagger. Это позволит конструктору приложений логики обрабатывать входные и выходные данные для API. Без конечной точки Swagger конструктор сможет отображать входные и выходные данные только как непрозрачные объекты JSON.

Вот пример, в котором показано новое свойство `metadata.apiDefinitionUrl`:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

При размещении веб-API в **службе приложений** он будет автоматически отображаться в списке действий, доступных в конструкторе. В противном случае вам придется вставить URL-адрес напрямую. Конечная точка Swagger должна быть непроверенной, чтобы ее можно было использовать в конструкторе приложений логики (хотя вы можете защитить сам API-интерфейс с помощью любого метода, поддерживаемого в Swagger).

### Использование уже развернутых приложений API с версией 2015-08-01-preview

Если вы развернули приложение API ранее, его можно вызвать через действие **HTTP**.

Например, если вы используете Dropbox для просмотра списка файлов, возможно, в определении схемы версии **2014-12-01-preview** может содержаться что-то подобное этому:

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

Вы можете создать действие HTTP, эквивалентное приведенному ниже (раздел параметров в определении приложения логики остается без изменений).

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
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
| --------------- | -----------  |
| `type` | `Http` вместо `APIapp` |
| `metadata.apiDefinitionUrl` | Если вы хотите использовать это действие в конструкторе приложений логики, возможно, вам потребуется включить конечную точку метаданных. Состоит из следующих компонентов: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Состоит из следующих компонентов: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Всегда `POST` |
| `inputs.body` | Идентично параметрам приложения API. | 
| `inputs.authentication` | Идентично проверке подлинности приложения API. |

Этот подход должен работать для всех действий приложений API. Однако следует помнить, что предыдущие приложения API больше не поддерживаются, поэтому следует выбрать один из двух других вариантов, указанных выше (управляемый интерфейс API или размещение пользовательского интерфейса веб-API).

## 2\. Переименование действия Repeat в Foreach

За время использования предыдущей версии схемы мы получили множество отзывов клиентов о том, что название **Repeat** приводит к путанице и не отображает того, что действие применяется к каждому циклу. В результате мы переименовали это действие в **Foreach**. Например:

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

Теперь оно записывается так:

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

Ранее функция `@repeatItem()` использовалась для указания того, что с текущим элементом выполняется итерация. Теперь она упрощена и выглядит так: `@item()`.

### Ссылка на выходные данные действия Foreach
Для дальнейшего упрощения выходные данные действий **Foreach** не помещаются в объект **repeatItems**. То есть ранее выходные данные вышеупомянутого действия Repeat выглядели так:

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
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Теперь они будут иметь следующий вид:

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
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Чтобы, ссылаясь на эти выходные данные, получить текст действия, вам пришлось бы сделать следующее:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Теперь вместо этого можно сделать следующее:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Эти изменения сопряжены с удалением функций `@repeatItem()`, `@repeatBody()` и `@repeatOutputs()`.

## 3\. Встроенный прослушиватель HTTP 
Возможности прослушивателя HTTP теперь являются встроенными, поэтому больше не нужно развертывать приложение API прослушивателя HTTP. Ознакомьтесь с [подробными инструкциями о том, как сделать конечную точку приложения логики вызываемой](app-service-logic-http-endpoint.md).

После внесения этих изменений функция `@accessKeys()` была удалена и заменена функцией `@listCallbackURL()` для получения конечной точки (при необходимости). Кроме того, теперь в приложении логики необходимо определить хотя бы один триггер. Если вы хотите запустить (`/run`) рабочий процесс, необходим один из таких триггеров: `manual`, `apiConnectionWebhook` или `httpWebhook`.

## 4\. Вызов дочерних рабочих процессов

Ранее для вызова дочерних рабочих процессов следовало перейти к нужному процессу, получить маркер доступа и вставить его в определение приложения логики, которое должно вызвать этот дочерний рабочий процесс. В новой версии схемы ядро приложений логики автоматически создает SAS в среде выполнения для дочернего рабочего процесса. Это означает, что вам не нужно вставлять в определение никакие секреты. Пример:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Второе улучшение заключается в том, что дочерние процессы получают полный доступ к входящим запросам. Это означает, что вы можете передавать параметры в раздел *queries* и объект *headers*, а также полностью определять весь текст.

Наконец, внесены изменения, необходимые для дочернего рабочего процесса. Раньше дочерний рабочий процесс можно было вызвать только напрямую. Теперь, чтобы родительский рабочий процесс мог выполнить вызов, необходимо определить конечную точку триггера в дочернем рабочем процессе. В общих чертах это означает, что вы добавите триггер типа **manual** и будете использовать его в определении родительского рабочего процесса. Обратите внимание, что свойство `host` содержит элемент `triggerName`, так как всегда следует указывать вызываемый триггер.

## Другие изменения

### Новое свойство queries
Все типы действий теперь поддерживают новые входные данные, называемые **queries**. Это может быть структурированный объект, который избавит вас от необходимости собирать строку вручную.

### Переименование функции parse()
В ближайшее время мы добавим дополнительные типы содержимого, поэтому функция `parse()` теперь переименована и называется так: `json()`.

## Ожидается в ближайшее время: API-интерфейсы для интеграции Enterprise
Сейчас мы еще не располагаем управляемыми версиями API-интерфейсов интеграции Enterprise (например, AS2). Скоро они станут доступными, как описано в [стратегическом плане](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). А пока вы можете использовать существующие развернутые API-интерфейсы BizTalk с помощью действия HTTP, как описано выше в разделе "Использование уже развернутых приложений API".

<!---HONumber=AcomDC_0727_2016-->
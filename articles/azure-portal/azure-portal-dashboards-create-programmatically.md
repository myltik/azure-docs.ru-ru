---
title: Создание панелей мониторинга Azure программными средствами | Документация Майкрософт
description: В этой статье описывается создание панелей мониторинга Azure программными средствами.
services: azure-portal
documentationcenter: ''
author: adamab
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: 8670d25e10b58c40b9d0807de1db88c3296b193d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164391"
---
# <a name="programmatically-create-azure-dashboards"></a>Создание панелей мониторинга Azure программными средствами

В этой статье подробно описывается процесс создания и публикации панелей мониторинга Azure программными средствами. Ниже представлена панель мониторинга, которая используется в этой статье.

![пример панели мониторинга](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Обзор

Общие панели мониторинга в Azure являются [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), так же как и виртуальные машины или учетные записи хранения.  Таким образом, ими можно программно управлять с помощью [интерфейсов API REST Azure Resource Manager](/rest/api/), [Azure CLI](https://docs.microsoft.com/cli/azure), [команд Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0) и многих других возможностей [портала Azure](https://portal.azure.com), основанных на этих API для упрощенного управления ресурсами.  

Каждый из этих API-интерфейсов и средств предлагает способы создания, перечисления, извлечения, изменения и удаления ресурсов.  Так как панели мониторинга являются ресурсами, вы можете выбрать для использования API-интерфейс или средство на свое усмотрение.

Независимо от используемого средства, вам необходимо создать представление JSON объекта панели мониторинга, чтобы затем вызвать любой API для создания ресурсов. Такой объект содержит сведения об элементах (то есть плитках) на панели мониторинга. В частности, размеры, положение, ресурсы, к которым они привязаны, а также пользовательские настройки.

Проще всего создать такой документ JSON, используя [портал](https://portal.azure.com/) для интерактивного добавления и размещения плиток. Затем вы можете экспортировать документ JSON. И наконец — создать шаблон из полученного результата для дальнейшего использования в скриптах, программах и средствах развертывания.

## <a name="create-a-dashboard"></a>Создание панели мониторинга

Чтобы создать панель мониторинга, используйте команду "Создать панель мониторинга" на главном экране портала.

![команда "Создать панель мониторинга"](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Для поиска и добавления плиток вы затем можете использовать коллекцию плиток. Чтобы добавить плитки, вам нужно просто перетащить их. Некоторые плитки поддерживают изменение размера. Для этого используется маркер перетаскивания. Другие же поддерживают фиксированные размеры, которые можно просмотреть в контекстном меню.

### <a name="drag-handle"></a>Маркер перетаскивания
![маркер перетаскивания](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Фиксированные размеры, поддерживаемые контекстным меню
![контекстное меню размеров](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Общий доступ к панели мониторинга

После настройки панели мониторинга по своему усмотрению вы можете опубликовать ее (с помощью команды "Общий доступ"), а затем использовать обозреватель ресурсов, чтобы получить документ JSON.

![команда общего доступа](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Если вы щелкните команду "Общий доступ", появится диалоговое окно с предложением выбрать подписку и группу ресурсов для публикации. Имейте в виду, что [у вас должен быть доступ на запись](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) в отношении выбранной подписки и группы ресурсов.

![совместное использование и доступ](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Получение представления JSON панели мониторинга

Публикация занимает несколько секунд.  После ее завершения необходимо перейти в [обозреватель ресурсов](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade), чтобы получить документ JSON.

![обозреватель ресурсов браузера](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Из обозревателя ресурсов перейдите в выбранные подписку и группу ресурсов. Затем щелкните последний опубликованный ресурс панели мониторинга для отображения документа JSON.

![отображение документа JSON в обозревателе ресурсов](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Создание шаблона из документа JSON

Далее необходимо создать шаблон из этого документа JSON для его повторного использования программным способом с помощью соответствующих API-интерфейсов управления ресурсами, программ командной строки и других средств портала.

Чтобы создать шаблон, не требуется комплексное понимание структуры документа JSON панели мониторинга. В большинстве случаев достаточно сохранить структуру и конфигурацию каждой плитки, а затем выполнить параметризацию набора ресурсов Azure, на которые они ссылаются. Просмотрите экспортированную панель мониторинга JSON и выполните поиск всех вхождений идентификаторов ресурсов Azure. Наш пример панели мониторинга содержит несколько плиток, которые указывают на одну виртуальную машину Azure. Так происходит, потому что панель мониторинга ориентирована только на отдельный ресурс. Если выполнить в примере JSON (содержится в последних разделах статьи) поиск по запросу "/subscriptions", вы найдете несколько вхождений этого идентификатора.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Чтобы опубликовать эту панель мониторинга для любой виртуальной машины в будущем, вам необходимо параметризовать каждое вхождение этой строки в документе JSON. 

Есть две разновидности API-интерфейсов для создания ресурсов в Azure. [Императивные интерфейсы API](https://docs.microsoft.com/rest/api/resources/resources), позволяющие создать один ресурс за раз, и система [развертывания на основе шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), которую можно использовать для оркестрации создания нескольких зависимых ресурсов с помощью одного вызова API. Второй вариант изначально поддерживает параметризацию и создание шаблонов, поэтому мы используем его для примера.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Программное создание панели мониторинга из шаблона с помощью развертывания шаблона

Azure предлагает возможность оркестрации развертывания нескольких ресурсов. Вы можете создать шаблон развертывания, который выражает набор ресурсов для развертывания, а также связи между ними.  Для каждого ресурса используется единый формат JSON, как и при их последовательном создании. Разница заключается в том, что [язык шаблона](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) включает несколько понятий, например переменные, параметры, базовые функции и другие понятия. Такой расширенный синтаксис поддерживается только в контексте развертывания шаблона и не работает в сочетании с императивными интерфейсами API, о которых было сказано ранее.

При выборе этого варианта параметризацию следует выполнить с помощью синтаксиса параметра шаблона.  Вы замените все экземпляры найденных идентификаторов ресурсов, как показано ниже.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Пример свойства JSON с жестко заданным идентификатором ресурса
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Пример свойства JSON, преобразованный в параметризованную версию на основе параметров шаблона

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

Вам также нужно объявить некоторые необходимые метаданные шаблона и параметры в верхней части шаблона JSON следующим образом:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__В конце этой статьи вы увидите комплексный работающий шаблон.__

После создания шаблона вы можете его развернуть с помощью [интерфейсов API REST](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create) или [страницы развертывания шаблона портала](https://portal.azure.com/#create/Microsoft.Template).

Мы представляем две версии примера документа JSON панели мониторинга. Первая — версия, экспортированная с портала, которая уже была привязана к ресурсу. Вторая — версия шаблона, которую можно программно привязать к любой виртуальной машине и развернуть с помощью Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Представление JSON примера панели мониторинга (до создания шаблона)

Если следовать инструкциям выше, чтобы получить представление JSON развернутой панели мониторинга, вы увидите представление, показанное ниже. Обратите внимание на жестко заданные идентификаторы ресурсов, которые показывают, что эта панель мониторинга указывает на определенную виртуальную машину Azure.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Представление шаблона примера панели мониторинга

В версии шаблона панели мониторинга определены три параметра — __virtualMachineName__, __virtualMachineResourceGroup__ и __dashboardName__.  Они позволяют настроить эту панель мониторинга для другой виртуальной машины Azure при каждом развертывании. Параметризованные идентификаторы выделены, чтобы показать, что эту панель мониторинга можно программно настроить и развернуть для любой виртуальной машины Azure. Самый простой способ протестировать эту возможность — скопировать шаблон ниже и вставить его на [страницу развертывания шаблона на портале Azure](https://portal.azure.com/#create/Microsoft.Template). 

В этом примере панель мониторинга развертывается сама по себе, но язык шаблонов позволяет развертывать несколько ресурсов и в то же время объединять одну или несколько панелей мониторинга. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
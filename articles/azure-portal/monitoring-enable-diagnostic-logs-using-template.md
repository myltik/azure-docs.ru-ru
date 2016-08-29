.<properties
	pageTitle="Автоматическое включение параметров диагностики с помощью шаблона Resource Manager | Microsoft Azure"
	description="Узнайте, как использовать шаблон Resource Manager для создания параметров диагностики, которые позволят передавать журналы диагностики в концентраторы событий или сохранять их в учетной записи хранения."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

.<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Автоматическое включение параметров диагностики при создании ресурса из шаблона Resource Manager
В этой статье мы покажем, как применить [шаблон Azure Resource Manager](../resource-group-authoring-templates.md) для настройки параметров диагностики при создании ресурса. С помощью этого шаблона вы сможете запускать потоковую передачу журналов диагностики и метрик в концентраторы событий или архивировать их в учетной записи хранения при создании ресурса.

Для разных типов ресурсов журналы диагностики включаются с помощью шаблона Resource Manager по-разному.

- **Невычислительные** ресурсы (например, группы безопасности сети, Logic Apps или служба автоматизации) используют [параметры диагностики, описанные в этой статье](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Вычислительные** ресурсы (на основе WAD/LAD) используют [файл конфигурации WAD/LAD, описанный в этой статье](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

В этой статьей объясняется, как настроить диагностику с помощью каждого из этих методов.

Основные этапы:

1. Создайте шаблон в виде файла JSON, который описывает, как создать ресурс и включить диагностику.
2. [Разверните шаблон с помощью любого метода развертывания](../resource-group-template-deploy.md).

Ниже приведены примеры файла JSON шаблона для создания вычислительных и невычислительных ресурсов.

## Шаблон для невычислительных ресурсов
Для невычислительных ресурсов выполните два действия.

1. Добавьте в большой двоичный объект параметров параметры имени учетной записи хранения и идентификатора правила служебной шины (для хранения журналов диагностики в учетной записи хранения и (или) для потоковой передачи журналов в концентраторы событий).

    ```
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    }
    ```
2. Добавьте ресурс с типом `[resource namespace]/providers/diagnosticSettings` в массив ресурсов того ресурса, для которого нужно включить журналы диагностики.

    ```
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

Свойства большого двоичного объекта для параметров диагностики соответствуют [формату, который описан в этой статье](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Ниже приведен полный пример, в котором создается группа безопасности сети, а также настраивается потоковая передача в концентраторы событий и хранение в учетной записи хранения.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nsgName": {
            "type": "string",
			"metadata": {
				"description": "Name of the NSG that will be created."
			}
        },
		"storageAccountName": {
			"type": "string",
			"metadata": {
				"description":"Name of the Storage Account in which Diagnostic Logs should be saved."
			}
		},
		"serviceBusRuleId": {
			"type": "string",
			"metadata": {
				"description":"Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
			}
		}
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "apiVersion": "2016-03-30",
            "location": "westus",
            "properties": {
                "securityRules": []
            },
            "resources": [
				{
					"type": "providers/diagnosticSettings",
					"name": "Microsoft.Insights/service",
					"dependsOn": [
						"[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
					],
					"apiVersion": "2015-07-01",
					"properties": {
						"storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
                        "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
						"logs": [
							{
								"category": "NetworkSecurityGroupEvent",
								"enabled": true,
								"retentionPolicy": {
									"days": 0,
									"enabled": false
								}
							},
                            {
								"category": "NetworkSecurityGroupRuleCounter",
								"enabled": true,
								"retentionPolicy": {
									"days": 0,
									"enabled": false
								}
							}
						]
					}
				}
			],
            "dependsOn": []
        }
    ]
}
```

## Шаблон для вычислительных ресурсов
Чтобы включить диагностику для вычислительных ресурсов, например виртуальной машины или Service Fabric, выполните эти действия.

1. Добавьте расширение системы диагностики Azure в определение ресурсов для виртуальной машины.
2. Укажите в качестве параметра учетную запись хранения и (или) концентратор событий.
3. Добавьте содержимое XML-файла WADCfg в свойство XMLCfg, правильно экранируя все XML-символы.

> [AZURE.WARNING] Этот этап может оказаться непростой задачей. [В этой статье](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) приводится пример разбиения схемы конфигурации диагностики на переменные, которые правильно экранируются и форматируются.

Весь процесса описан [в этом документе](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md) с примерами.


## Дальнейшие действия
- [Дополнительные сведения о журналах диагностики Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Потоковая передача журналов диагностики Azure в концентраторы событий](./monitoring-stream-diagnostic-logs-to-event-hubs.md)

<!---HONumber=AcomDC_0817_2016-->
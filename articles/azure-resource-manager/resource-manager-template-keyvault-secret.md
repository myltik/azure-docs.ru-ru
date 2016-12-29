---
title: "Шаблон Resource Manager для секрета в хранилище ключей | Документация Майкрософт"
description: "В этой статье демонстрируется схема диспетчера ресурсов для развертывания секретных кодов хранилища ключей с помощью шаблона."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: fd12bc81-b609-4ca8-b7e0-ee1049d70ab3
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 885a9b09685982e9bae6b50f0dbc3edd21819859


---
# <a name="key-vault-secret-template-schema"></a>Схема шаблона секретного кода хранилища ключей
Создает секретный код, который хранится в хранилище ключей. Этот тип ресурса часто развертывается как дочерний ресурс [хранилища ключей](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Формат схемы
Чтобы создать секретный код хранилища ключей, добавьте в шаблон указанную ниже схему. Секретный код можно определить либо как дочерний ресурс хранилища ключей, либо как ресурс верхнего уровня. Его можно определить как дочерний ресурс, если хранилище ключей развертывается в том же шаблоне. Определять секретный код как ресурс верхнего уровня необходимо, если хранилище ключей не развертывается в том же шаблоне или если вам нужно создать несколько секретных кодов, используя тип ресурса в циклической конструкции. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| --- | --- |
| type |Перечисление.<br />Обязательно<br />**secrets** (при развертывании в качестве дочернего ресурса хранилища ключей) или<br /> **Microsoft.KeyVault/vaults/secrets** (при развертывании в качестве ресурса верхнего уровня).<br /><br />Тип создаваемого ресурса. |
| версия_API |Перечисление.<br />Обязательно<br />**2015-06-01** или **2014-12-19-preview**.<br /><br />Версия API, которая будет использована для создания ресурса. |
| Имя |Строка<br />Обязательно<br />Одно слово при развертывании в качестве дочернего ресурса хранилища ключей либо значение в формате **{имя_хранилища_ключей}/{имя_секрета}** при развертывании в качестве ресурса верхнего уровня, добавляемого в существующее хранилище ключей.<br /><br />Имя создаваемого секрета. |
| properties |Объект<br />Обязательно<br />[объект properties](#properties)<br /><br />Объект, который указывает значение создаваемого секрета. |
| Свойство dependsOn |Массив,<br />Необязательно<br />Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов.<br /><br />Коллекция ресурсов, от которых зависит эта связь. Если хранилище ключей разворачивается в том же шаблоне, включите в этот элемент имя хранилища ключей, чтобы оно было развернуто первым. |

<a id="properties" />

### <a name="properties-object"></a>объект properties
| Имя | Значение |
| --- | --- |
| Значение |Строка<br />Обязательно<br /><br />Значение секрета для хранения в хранилище ключей. Для передачи значения этого свойства используйте параметр типа **securestring**. |

## <a name="examples"></a>Примеры
Код в первом примере разворачивает секретный код как дочерний ресурс хранилища ключей.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Код во втором примере разворачивает секретный код как ресурс верхнего уровня, хранящийся в существующем хранилище ключей.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Пример ссылки на секрет хранилища ключей при развертывании шаблонов см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).




<!--HONumber=Nov16_HO3-->



<properties
   pageTitle="Шаблон диспетчера ресурсов для секретного кода в хранилище ключей | Microsoft Azure"
   description="В этой статье демонстрируется схема диспетчера ресурсов для развертывания секретных кодов хранилища ключей с помощью шаблона."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# Схема шаблона секретного кода хранилища ключей

Создает секретный код, который хранится в хранилище ключей. Этот тип ресурса часто развертывается как дочерний ресурс [хранилища ключей](resource-manager-template-keyvault.md).

## Формат схемы

Чтобы создать секретный код хранилища ключей, добавьте в шаблон указанную ниже схему. Секретный код можно определить либо как дочерний ресурс хранилища ключей, либо как ресурс верхнего уровня. Секретный код можно определить как дочерний ресурс, если хранилище ключей развертывается в том же шаблоне. Определять секретный код как ресурс верхнего уровня необходимо, если хранилище ключей не развертывается в том же шаблоне или если вам нужно создать несколько секретных кодов, используя тип ресурса в циклической конструкции.

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | Как дочерний ресурс хранилища ключей:<br />**secrets**<br /><br />Как ресурс верхнего уровня:<br />**Microsoft.KeyVault/vaults/secrets** | Тип создаваемого ресурса. |
| версия\_API | enum | Да | **2015-06-01** <br /> **2014-12-19-предварительная версия** | Версия API, которая будет использована для создания ресурса. | 
| name | строка | Да | | Имя создаваемого секретного кода. Если секретный код развертывается как дочерний ресурс хранилища ключей, просто укажите имя секретного кода. При развертывании секретного кода как ресурса верхнего уровня имена должны иметь формат **{имя-ключа-хранилища}/{имя-секретного-кода}**. |
| properties | object | Да | (показано ниже) | Объект, определяющий значение создаваемого секретного кода. |
| Свойство dependsOn | array | Нет | Разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. | Коллекция ресурсов, от которых зависит эта связь. Если хранилище ключей разворачивается в том же шаблоне, включите в этот элемент имя хранилища ключей, чтобы оно было развернуто первым. |

### объект properties

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| value | string | Да | | Хранилище секретных кодов для хранения в хранилище ключей. Для передачи значения этого свойства используйте параметр типа **securestring**. |

	
## Примеры

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
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
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
                "tags": { "displayName": "secret" },
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


## Дальнейшие действия

- Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).
- Пример ссылки на секретный код хранилища ключей при развертывании шаблонов см. в статье [ Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0107_2016-->
<properties
   pageTitle="Шаблон диспетчера ресурсов для хранилища ключей | Microsoft Azure"
   description="В этой статье демонстрируется схема диспетчера ресурсов для развертывания хранилища ключей с помощью шаблона."
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
   ms.date="02/23/2016"
   ms.author="tomfitz"/>

# Схема шаблона хранилища ключей

Создает хранилище ключей.

## Формат схемы

Чтобы создать хранилище ключей, добавьте следующую схему в раздел ресурсов шаблона.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | **Microsoft.KeyVault/vaults** | Тип создаваемого ресурса. |
| версия\_API | enum | Да | **2015-06-01** <br /> **2014-12-19-предварительная версия** | Версия API, которая будет использована для создания ресурса. | 
| name | строка | Да | | Имя создаваемого хранилища ключей. Имя должно быть уникальным в Azure. Мы рекомендуем использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring) в рамках соглашения об именовании, как показано в примере ниже. |
| location | строка | Да | Определить допустимые регионы можно в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions). | Регион для размещения хранилища ключей. |
| properties | object | Да | ([показано ниже](#properties)) | Объект, который определяет тип создаваемого хранилища ключей. |
| ресурсов | array | Нет | [Секреты хранилища ключей](resource-manager-template-keyvault-secret.md) | Дочерние ресурсы для хранилища ключей. |

<a id="properties" />
### объект properties

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | Логическое | Нет | Значение **true** или **false** | Указывает, позволяет ли хранилище развертывать виртуальную машину или Service Fabric. |
| enabledForTemplateDeployment | Логическое | Нет | Значение **true** или **false** | Указывает, позволяет ли хранилище использовать развертывания шаблона диспетчера ресурсов. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md). |
| enabledForVolumeEncryption | Логическое | Нет | Значение **true** или **false** | Указывает, позволяет ли хранилище шифровать тома. |
| tenantId | string | Да | Глобальный уникальный идентификатор | Идентификатор клиента для подписки. Его можно извлечь с помощью командлета PowerShell **Get AzureRMSubscription**. |
| accessPolicies | array | Да | ([показано ниже](#accesspolicies)) | Массив, содержащий до 16 объектов и определяющий разрешения для пользователя или субъекта-службы. |
| sku | object | Да | ([показано ниже](#sku)) | SKU для хранилища ключей. |

<a id="accesspolicies" />
### Объект properties.accessPolicies

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | string | Да | Глобальный уникальный идентификатор | Идентификатор клиента Azure Active Directory, содержащего свойство **objectId** этой политики доступа |
| objectId | string | Да | Глобальный уникальный идентификатор | Идентификатор объекта пользователя AAD или субъекта-службы, который будет иметь доступ в хранилище. Данное значение можно получить с помощью командлета **Get AzureRMADUser** или **Get AzureRMADServicePrincipal**. |
| permissions | object | Да | ([показано ниже](#permissions)) | Разрешения, предоставленные этому хранилищу в отношении объекта Active Directory. |

<a id="permissions" />
### Объект properties.accessPolicies.permissions

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| ключи | array | Да | Разделенный запятыми список из следующих значений:<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | Разрешения, предоставленные ключам хранилища в отношении данного объекта Active Directory. Это значение необходимо указать как массив допустимых значений. |
| секретные коды | array | Да | Разделенный запятыми список из следующих значений:<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | Разрешения, предоставленные секретным кодам хранилища в отношении данного объекта Active Directory. Это значение необходимо указать как массив допустимых значений. |

<a id="sku" />
### Объект properties.sku

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | Да | **standard**<br />**premium** | Уровень обслуживания используемого хранилища ключей. Хранилище уровня Standard поддерживает секретные коды и ключи, защищенные с помощью ПО. Хранилище уровня Premium поддерживает ключи, защищенные аппаратным модулем безопасности. |
| family | enum | Да | **ФАЙЛ ,** | Используемое семейство SKU. 
 
	
## Примеры

Код в приведенном ниже примере разворачивает хранилище ключей и секретный код.

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
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## Шаблоны быстрого запуска

Следующий шаблон быстрого запуска разворачивает хранилище ключей.

- [Создание хранилища ключей](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)


## Дальнейшие действия

- Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).
- Пример ссылки на секретный код хранилища ключей при развертывании шаблонов см. в статье [ Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0224_2016-->
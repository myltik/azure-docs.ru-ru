<properties
   pageTitle="Шаблон диспетчера ресурсов для хранилища ключей | Microsoft Azure"
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
   ms.date="04/04/2016"
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

| Имя | Значение |
| ---- | ---- | 
| type | Enum<br />Required<br />**Microsoft.KeyVault/vaults**<br /><br />Тип создаваемого ресурса. |
| версия\_API | Enum<br />Обязательно<br />**2015-06-01** или **2014-12-19-предварительная версия**<br /><br />Версия API, которая будет использована для создания ресурса. | 
| name | String<br />Обязательно<br />Имя должно быть уникальным в Azure.<br /><br />Имя создаваемого хранилища ключей. Для создания уникального имени в рамках соглашения об именовании рекомендуется использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring), как показано в примере ниже. |
| location | String<br />Обязательно<br />Допустимый регион для хранилищ ключей. Сведения об определении допустимых регионов см. в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions).<br /><br />Регион для размещения хранилища ключей. |
| properties | Object<br />Обязательно<br />[объект properties](#properties)<br /><br />Объект, который определяет тип создаваемого хранилища ключей. |
| ресурсов | Array<br />Необязательно<br />Допустимые значения: [Ресурсы секрета хранилища ключей](resource-manager-template-keyvault-secret.md)<br /><br />Дочерние ресурсы для хранилища ключей. |

<a id="properties" />
### объект properties

| Имя | Значение |
| ---- | ---- | 
| enabledForDeployment | Boolean<br />Необязательно<br />**true** или **false**<br /><br />Указывает, позволяет ли хранилище развертывать виртуальную машину или Service Fabric. |
| enabledForTemplateDeployment | Boolean<br />Необязательно<br />**true** или **false**<br /><br />Указывает, доступно ли хранилище для использования в развертываниях шаблонов диспетчера ресурсов. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md). |
| enabledForVolumeEncryption | Boolean<br />Необязательно<br />**true** или **false**<br /><br />Указывает, позволяет ли хранилище шифровать тома. |
| tenantId | String<br />Обязательно<br />**Глобальный уникальный идентификатор**<br /><br />Идентификатор клиента для подписки. Его можно извлечь с помощью командлета PowerShell [Get AzureRMSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) или команды Azure CLI **azure account show**. |
| accessPolicies | Array<br />Обязательно<br />[объект accessPolicies](#accesspolicies)<br /><br />Массив, содержащий до 16 объектов и определяющий разрешения для пользователя или субъекта-службы. |
| sku | Object<br />Обязательно<br />[объект sku](#sku)<br /><br />SKU для хранилища ключей. |

<a id="accesspolicies" />
### Объект properties.accessPolicies

| Имя | Значение |
| ---- | ---- | 
| tenantId | String<br />Обязательно<br />**Глобальный уникальный идентификатор**<br /><br />Идентификатор клиента Azure Active Directory, содержащего свойство **objectId** этой политики доступа. |
| objectId | String<br />Обязательно<br />**Глобальный уникальный идентификатор **<br /><br />Идентификатор объекта пользователя Azure Active Directory или субъекта-службы, который будет иметь доступ в хранилище. Значение можно получить из командлетов PowerShell [Get-AzureRMADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) или [Get-AzureRMADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) либо команд Azure CLI **azure ad user** или **azure ad sp**. |
| permissions | Object<br />Обязательно<br />[объект permissions](#permissions)<br /><br />Разрешения, предоставленные этому хранилищу в отношении объекта Active Directory. |

<a id="permissions" />
### Объект properties.accessPolicies.permissions

| Имя | Значение |
| ---- | ---- | 
| ключи | Array<br />Обязательно<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />Разрешения, предоставленные ключам хранилища в отношении данного объекта Active Directory. Это значение необходимо указать как массив одного или нескольких допустимых значений. |
| секретные коды | Array<br />Обязательно<br />**all**, **delete**, **get**, **list**, **set**<br /><br />Разрешения, предоставленные секретным кодам хранилища в отношении данного объекта Active Directory. Это значение необходимо указать как массив одного или нескольких допустимых значений. |

<a id="sku" />
### Объект properties.sku

| Имя | Значение |
| ---- | ---- | 
| name | Enum<br />Обязательно<br />**standard** или **premium** <br /><br />Уровень обслуживания используемого хранилища ключей. Хранилище уровня Standard поддерживает секретные коды и ключи, защищенные с помощью ПО. Хранилище уровня Premium поддерживает ключи, защищенные аппаратным модулем безопасности. |
| family | Enum<br />Обязательно<br />**A** <br /><br />Используемое семейство SKU. |
 
	
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

- [Создание хранилища ключей](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## Дальнейшие действия

- Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).
- Пример ссылки на секретный код хранилища ключей при развертывании шаблонов см. в статье [ Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0406_2016-->
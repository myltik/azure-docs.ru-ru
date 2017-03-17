---
title: "Шаблон Resource Manager для хранилища ключей | Документация Майкрософт"
description: "В этой статье демонстрируется схема диспетчера ресурсов для развертывания хранилища ключей с помощью шаблона."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>Схема шаблона хранилища ключей
Создает хранилище ключей.

## <a name="schema-format"></a>Формат схемы
Чтобы создать хранилище ключей, добавьте следующую схему в раздел ресурсов шаблона.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
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

## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| --- | --- |
| type |Перечисление.<br />Обязательно<br />**Microsoft.KeyVault/vaults**<br /><br />Тип создаваемого ресурса. |
| версия_API |Перечисление.<br />Обязательно<br />**2015-06-01** или **2014-12-19-preview**.<br /><br />Версия API, которая будет использована для создания ресурса. |
| Имя |Строка<br />Обязательно<br />Имя, которое является уникальным в Azure.<br /><br />Имя создаваемого хранилища ключей. Для создания уникального имени в рамках соглашения об именовании рекомендуется использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring) , как показано в примере ниже. |
| location |Строка<br />Обязательно<br />Допустимый регион для хранилищ ключей. Определить допустимые регионы можно в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions).<br /><br />Регион для размещения хранилища ключей. |
| properties |Объект<br />Обязательно<br />[объект properties](#properties)<br /><br />Объект, который определяет тип создаваемого хранилища ключей. |
| ресурсов |Массив,<br />Необязательно<br />Допустимые значения: [ресурсы секрета хранилища ключей](resource-manager-template-keyvault-secret.md).<br /><br />Дочерние ресурсы для хранилища ключей. |

<a id="properties" />

### <a name="properties-object"></a>объект properties
| Имя | Значение |
| --- | --- |
| enabledForDeployment |Логический<br />Необязательно<br />**true** или **false**.<br /><br />Указывает, позволяет ли хранилище развертывать виртуальную машину или Service Fabric. |
| enabledForTemplateDeployment |Логический<br />Необязательно<br />**true** или **false**.<br /><br />Указывает, позволяет ли хранилище использовать развертывания шаблона диспетчера ресурсов. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md). |
| enabledForDiskEncryption |Логический<br />Необязательно<br />**true** или **false**.<br /><br />Указывает, позволяет ли хранилище шифровать тома. |
| tenantId |Строка<br />Обязательно<br />**Глобальный уникальный идентификатор.**<br /><br />Идентификатор клиента для подписки. Его можно извлечь с помощью командлета PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) или команды Azure CLI **azure account show** . |
| accessPolicies |Массив,<br />Обязательно<br />[Объект accessPolicies](#accesspolicies)<br /><br />Массив, содержащий до 16 объектов и определяющий разрешения для пользователя или субъекта-службы. |
| sku |Объект<br />Обязательно<br />[Объект sku](#sku)<br /><br />SKU для хранилища ключей. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>Объект properties.accessPolicies
| Имя | Значение |
| --- | --- |
| tenantId |Строка<br />Обязательно<br />**Глобальный уникальный идентификатор.**<br /><br />Идентификатор клиента Azure Active Directory, который содержит свойство **objectId** в этой политике доступа. |
| objectId |Строка<br />Обязательно<br />**Глобальный уникальный идентификатор.**<br /><br />Идентификатор объекта пользователя Azure Active Directory или субъекта-службы, который будет иметь доступ в хранилище. Значение можно получить с помощью командлета PowerShell [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) или [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx), а также команды Azure CLI **azure ad user** или **azure ad sp**. |
| permissions |Объект<br />Обязательно<br />[Объект permissions](#permissions)<br /><br />Разрешения, предоставленные этому хранилищу в отношении объекта Active Directory. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>Объект properties.accessPolicies.permissions
| Имя | Значение |
| --- | --- |
| ключи |Массив,<br />Обязательно<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**.<br /><br />Разрешения, предоставленные ключам хранилища в отношении объекта Active Directory. Это значение необходимо указать как массив одного или нескольких допустимых значений. |
| секретные коды |Массив,<br />Обязательно<br />**all**, **delete**, **get**, **list**, **set**.<br /><br />Разрешения, предоставленные секретным кодам хранилища в отношении объекта Active Directory. Это значение необходимо указать как массив одного или нескольких допустимых значений. |

<a id="sku" />

### <a name="propertiessku-object"></a>Объект properties.sku
| Имя | Значение |
| --- | --- |
| Имя |Перечисление.<br />Обязательно<br />**standard** или **premium**. <br /><br />Используемый уровень обслуживания хранилища ключей.  Хранилище уровня Standard поддерживает секретные коды и ключи, защищенные с помощью ПО.  Хранилище уровня Premium поддерживает ключи, защищенные аппаратным модулем безопасности. |
| family |Перечисление.<br />Обязательно<br />**A** <br /><br />Используемое семейство SKU. |

## <a name="examples"></a>Примеры
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
            "enableVaultForDiskEncryption": {
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
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
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

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска
Следующий шаблон быстрого запуска разворачивает хранилище ключей.

* [Создание хранилища ключей](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Пример ссылки на секрет хранилища ключей при развертывании шаблонов см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).



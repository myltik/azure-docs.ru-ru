---
title: "Использование управляемого удостоверения службы Azure в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как использовать управляемое удостоверение службы Azure в службе управления API."
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 55fac34a5eae169a3a4fd8c64c90c552fdb5df5a
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Использование управляемого удостоверения службы Azure в службе управления API Azure

> [!Note]
> Управляемые удостоверения службы для службы управления API Azure сейчас находятся на этапе предварительной версии.

Из этой статьи вы узнаете, как создать управляемое удостоверение службы для экземпляра службы управления API, а также как работать с другими ресурсами. Управляемое удостоверение службы, созданное службой Azure Active Directory (Azure AD), позволяет экземпляру службы управления API легко и безопасно работать с другими ресурсами, защищенными службой Azure AD, например Azure Key Vault. Этим удостоверением управляет платформа Azure, и для него не нужно подготавливать или сменять секреты. Дополнительные сведения об управляемых удостоверениях службы Azure см. в статье [Управляемое удостоверение службы (MSI) для ресурсов Azure](../active-directory/msi-overview.md).

## <a name="create-an-api-management-instance-with-an-identity-by-using-a-resource-manager-template"></a>Создание экземпляра службы управления API с удостоверением с помощью шаблона Resource Manager

Чтобы создать экземпляр службы управления API с удостоверением, добавьте в определение ресурса следующее свойство: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Это свойство указывает платформе Azure, что требуется создать удостоверение для экземпляра службы управления API и управлять им. 

Например, полный шаблон Azure Resource Manager может выглядеть так:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "[parameters('sku')]",
                "capacity": "[parameters('skuCount')]"
            },
            "properties": {
                "publisherEmail": "[parameters('publisherEmail')]",
                "publisherName": "[parameters('publisherName')]"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```

## <a name="obtain-a-certificate-from-azure-key-vault"></a>Получение сертификата из Azure Key Vault

В примере ниже показано, как получить сертификат из службы Azure Key Vault. Он содержит следующие шаги:

1. Создание экземпляра службы управления API с удостоверением.
2. Обновление политик доступа экземпляра Azure Key Vault и разрешение экземпляру службы управления API получать секреты из экземпляра Azure Key Vault.
3. Настройка имени личного домена для экземпляра службы управления API с помощью сертификата из экземпляра Key Vault.

### <a name="prerequisites"></a>предварительным требованиям
1. Решение Key Vault с PFX-сертификатом должно относиться к той же подписке Azure и той же группе ресурсов, что и служба управления API. Это требование для шаблона Azure Resource Manager. 
2. Тип содержимого секрета должен быть *application/x-pkcs12*. Вы можете использовать следующий скрипт для отправки сертификата:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Если версия объекта сертификата не указана, служба управления API автоматически получит более новую версию сертификата после ее передачи в Key Vault. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управляемых удостоверениях службы Azure приведены в следующих статьях:

* [Управляемое удостоверение службы (MSI) для ресурсов Azure](../active-directory/msi-overview.md)
* [Шаблоны диспетчера ресурсов Azure](https://github.com/Azure/azure-quickstart-templates)


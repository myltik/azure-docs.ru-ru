---
title: "Секрет хранилища ключей в шаблоне Resource Manager | Документация Майкрософт"
description: "Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование хранилища ключей для передачи защищенного значения параметра во время развертывания

Если необходимо передать защищенное значение (например, пароль) в качестве параметра во время развертывания, можно извлечь его из [Azure Key Vault](../key-vault/key-vault-whatis.md). Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Вам не нужно будет вручную вводить значение секрета при каждом развертывании ресурсов. Хранилище ключей не обязательно должно находится в той же подписке, что и группа ресурсов, развертывание в которую выполняется. При указании ссылки на хранилище ключей можно добавить идентификатор подписки.

При создании хранилища ключей задайте для свойства *enabledForTemplateDeployment* значение *true*. Таким образом вы разрешите доступ из шаблонов Resource Manager во время развертывания.  

## <a name="deploy-a-key-vault-and-secret"></a>Развертывание хранилища ключей и секретного кода

Чтобы создать хранилище ключей и секрет, используйте Azure CLI или PowerShell. Обратите внимание, что для хранилища ключей включено развертывание шаблона. 

Для интерфейса командной строки Azure:

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Для PowerShell:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Разрешение доступа к секрету

При использовании как нового, так и существующего хранилища ключей убедитесь, что у пользователя, который развертывает шаблон, есть доступ к секрету. Пользователь, развертывающий шаблон, который ссылается на секрет, должен иметь разрешение `Microsoft.KeyVault/vaults/deploy/action` для хранилища ключей. Оно имеется у ролей [Владелец](../active-directory/role-based-access-built-in-roles.md#owner) и [Участник](../active-directory/role-based-access-built-in-roles.md#contributor). Можно также создать [пользовательскую роль](../active-directory/role-based-access-control-custom-roles.md), которая предоставляет данное разрешение, и добавить пользователя в эту роль. Дополнительные сведения о назначении пользователю роли см. в статье [Назначение пользователю ролей администратора в Azure Active Directory](../active-directory/active-directory-users-assign-role-azure-portal.md).


## <a name="reference-a-secret-with-static-id"></a>Ссылка на секрет со статическим идентификатором

Шаблон, получающий секрет хранилища ключей, аналогичен любому другому шаблону. Это происходит, потому что **вы ссылаетесь на хранилище ключей в файле параметров, а не в шаблоне**. Например, в приведенном ниже шаблоне развертывается база данных SQL, которая содержит пароль администратора. В качестве параметра пароля задается защищенная строка. Но в шаблоне не указывается источник этого значения.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

Теперь создайте файл параметров для предыдущего шаблона. В файле параметров укажите параметр, который совпадает с именем параметра в шаблоне. Для значения параметра используйте ссылку на секрет из хранилища ключей. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В приведенном ниже примере секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>Ссылка на секрет с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. В этом случае вы не можете жестко задать идентификатор ресурса в файле параметров. К сожалению, динамически создать идентификатор ресурса в файле параметров невозможно, так как выражения шаблонов в файле параметров не разрешены.

Для динамического создания идентификатора ресурса для секрета хранилища ключей необходимо переместить ресурс, для которого требуется секрет, во вложенный шаблон. Добавьте вложенный шаблон в главный шаблон и передайте параметр, содержащий динамически созданный идентификатор ресурса.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).


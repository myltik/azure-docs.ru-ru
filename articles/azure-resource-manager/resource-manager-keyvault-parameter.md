---
title: "Секрет Key Vault в шаблоне Azure Resource Manager | Документация Майкрософт"
description: "Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания

Если необходимо передать защищенное значение (например, пароль) в качестве параметра во время развертывания, можно извлечь его из [Azure Key Vault](../key-vault/key-vault-whatis.md). Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Вам не нужно будет вручную вводить значение секрета при каждом развертывании ресурсов. Хранилище ключей не обязательно должно находится в той же подписке, что и группа ресурсов, развертывание в которую выполняется. При указании ссылки на хранилище ключей можно добавить идентификатор подписки.

При создании хранилища ключей задайте для свойства *enabledForTemplateDeployment* значение *true*. Таким образом вы разрешите доступ из шаблонов Resource Manager во время развертывания.

## <a name="deploy-a-key-vault-and-secret"></a>Развертывание хранилища ключей и секретного кода

Чтобы создать хранилище ключей и секрет, используйте Azure CLI или PowerShell. Обратите внимание, что для хранилища ключей включено развертывание шаблона. 

Для интерфейса командной строки Azure:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Для PowerShell:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Разрешение доступа к секрету

При использовании как нового, так и существующего хранилища ключей убедитесь, что у пользователя, который развертывает шаблон, есть доступ к секрету. Пользователь, развертывающий шаблон, который ссылается на секрет, должен иметь разрешение `Microsoft.KeyVault/vaults/deploy/action` для хранилища ключей. Оно имеется у ролей [Владелец](../active-directory/role-based-access-built-in-roles.md#owner) и [Участник](../active-directory/role-based-access-built-in-roles.md#contributor).

## <a name="reference-a-secret-with-static-id"></a>Ссылка на секрет со статическим идентификатором

Шаблон, получающий секрет хранилища ключей, аналогичен любому другому шаблону. Это происходит, потому что **вы ссылаетесь на хранилище ключей в файле параметров, а не в шаблоне**. Например, в приведенном ниже шаблоне развертывается база данных SQL, которая содержит пароль администратора. В качестве параметра пароля задается защищенная строка. Но в шаблоне не указывается источник этого значения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Теперь создайте файл параметров для предыдущего шаблона. В файле параметров укажите параметр, который совпадает с именем параметра в шаблоне. Для значения параметра используйте ссылку на секрет из хранилища ключей. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В приведенном ниже примере секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Теперь разверните шаблон и передайте файл параметров. Для интерфейса командной строки Azure:

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Ссылка на секрет с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. В этом случае вы не можете жестко задать идентификатор ресурса в файле параметров. К сожалению, динамически создать идентификатор ресурса в файле параметров невозможно, так как выражения шаблонов в файле параметров не разрешены.

Для динамического создания идентификатора ресурса для секрета хранилища ключей необходимо переместить ресурс, для которого требуется секрет, во вложенный шаблон. Добавьте вложенный шаблон в главный шаблон и передайте параметр, содержащий динамически созданный идентификатор ресурса. Вложенный шаблон должен быть доступен через внешний URI. В оставшейся части этой статьи предполагается, что вы добавили предыдущий шаблон в учетную запись хранения и он доступен через URI — `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
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
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Разверните предыдущий шаблон и укажите значения для параметров.

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

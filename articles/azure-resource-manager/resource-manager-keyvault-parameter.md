---
title: Секрет Key Vault в шаблоне Azure Resource Manager | Документация Майкрософт
description: Демонстрирует передачу секретного кода из хранилища ключей в виде параметра при развертывании.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/11/2018
ms.author: tomfitz
ms.openlocfilehash: 6a6c1f10b5a46633785d9c26a766df9334fe1cb0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359100"
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

При использовании как нового, так и существующего хранилища ключей убедитесь, что у пользователя, который развертывает шаблон, есть доступ к секрету. Пользователь, развертывающий шаблон, который ссылается на секрет, должен иметь разрешение `Microsoft.KeyVault/vaults/deploy/action` для хранилища ключей. Оно имеется у ролей [Владелец](../role-based-access-control/built-in-roles.md#owner) и [Участник](../role-based-access-control/built-in-roles.md#contributor).

## <a name="reference-a-secret-with-static-id"></a>Ссылка на секрет со статическим идентификатором

Шаблон, получающий секрет хранилища ключей, аналогичен любому другому шаблону. Это происходит, потому что **вы ссылаетесь на хранилище ключей в файле параметров, а не в шаблоне**. На следующем рисунке показано, как файл параметров ссылается на секрет и передает это значение в шаблон.

![Статический идентификатор](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Например, в [приведенном ниже шаблоне](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) развертывается база данных SQL, которая содержит пароль администратора. В качестве параметра пароля задается защищенная строка. Но в шаблоне не указывается источник этого значения.

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

Теперь создайте файл параметров для предыдущего шаблона. В файле параметров укажите параметр, который совпадает с именем параметра в шаблоне. Для значения параметра используйте ссылку на секрет из хранилища ключей. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В [приведенном ниже файле параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json) секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение. Скопируйте этот файл локально и задайте идентификатор подписки, имя хранилища и имя сервера SQL Server.

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

Если необходимо использовать версию секрета, отличную от текущей, примените свойство `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Теперь разверните шаблон и передайте файл параметров. Можно воспользоваться примером шаблона из GitHub, однако необходимо использовать локальный файл параметров со значениями, заданными для вашей среды.

Для интерфейса командной строки Azure:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Ссылка на секрет с динамическим идентификатором

Выше мы рассмотрели способ передачи статического идентификатора ресурса для секрета хранилища ключей. Однако в некоторых сценариях вам нужно использовать ссылку на секретный код хранилища ключей, который изменяется в зависимости от текущего развертывания. В этом случае вы не можете жестко задать идентификатор ресурса в файле параметров. К сожалению, динамически создать идентификатор ресурса в файле параметров нельзя, так как выражения шаблонов в файле параметров не разрешены.

Для динамического создания идентификатора ресурса для секрета хранилища ключей необходимо переместить ресурс, для которого требуется секрет, в связанный шаблон. Добавьте связанный шаблон в родительский шаблон и передайте параметр, содержащий динамически созданный идентификатор ресурса. На следующем рисунке показано, как параметр в связанном шаблоне ссылается на секрет.

![Динамический идентификатор](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Связанный шаблон должен быть доступен через внешний URI. Как правило, шаблон добавляется в учетную запись хранения, а доступ к нему осуществляется через URI, например `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

[Следующий шаблон](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) динамически создает идентификатор хранилища ключей и передает его в качестве параметра. Он ссылается на [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) в GitHub.

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
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
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

Разверните предыдущий шаблон и укажите значения для параметров. Можно воспользоваться примером шаблона из GitHub, однако необходимо указать значения параметров для вашей среды.

Для интерфейса командной строки Azure:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о хранилищах ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](../key-vault/key-vault-get-started.md).
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

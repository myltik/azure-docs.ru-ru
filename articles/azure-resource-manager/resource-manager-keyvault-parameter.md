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
ms.date: 11/11/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fa688748be96aa614b46a218e19a0e771b908baf


---
# <a name="pass-secure-values-during-deployment"></a>Передача безопасных значений в процессе развертывания

Если в процессе развертывания в качестве параметра необходимо передать защищенное значение (например, пароль), его можно сохранить как секрет в [хранилище ключей Azure](../key-vault/key-vault-whatis.md) и указать ссылку на это значение в файл параметров. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Вам не нужно будет вручную вводить значение секрета при каждом развертывании ресурсов.

## <a name="deploy-a-key-vault-and-secret"></a>Развертывание хранилища ключей и секретного кода

Чтобы узнать о развертывании хранилища ключей и секрета, ознакомьтесь со [схемой хранилища ключей](resource-manager-template-keyvault.md) и [схемой секрета хранилища ключей](resource-manager-template-keyvault-secret.md). При создании хранилища ключей задайте для свойства **enabledForTemplateDeployment** значение **true**, чтобы ссылки на это хранилище можно было использовать в других шаблонах Resource Manager. 

## <a name="reference-a-secret-with-static-id"></a>Ссылка на секретный код со статическим идентификатором

Ссылка на секрет добавляется в файл параметров, который передает значения в шаблон. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В приведенном ниже примере секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

Принимать секретный код должен параметр **securestring**. В следующем примере показаны соответствующие разделы шаблона, который развертывает SQL Server и требует пароля администратора.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```

Пользователь, развертывающий шаблон, который ссылается на секрет, должен иметь разрешение **Microsoft.KeyVault/vaults/deploy/action** для хранилища ключей. Оно имеется у ролей [Владелец](../active-directory/role-based-access-built-in-roles.md#owner) и [Участник](../active-directory/role-based-access-built-in-roles.md#contributor). Можно также создать [пользовательскую роль](../active-directory/role-based-access-control-custom-roles.md), которая предоставляет данное разрешение, и добавить пользователя в эту роль.

## <a name="reference-a-secret-with-dynamic-id"></a>Ссылка на секретный код с динамическим идентификатором

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
* Сведения об использовании хранилища ключей с виртуальной машиной см. в разделе [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).




<!--HONumber=Nov16_HO3-->



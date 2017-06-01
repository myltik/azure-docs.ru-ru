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
ms.date: 12/09/2016
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0bf872a44b8ed7cae53d2659aa7be878902130e9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Использование хранилища ключей для передачи защищенного значения параметра во время развертывания

Если необходимо передать защищенное значение (например, пароль) в качестве параметра во время развертывания, можно извлечь его из [Azure Key Vault](../key-vault/key-vault-whatis.md). Для получения значения нужно указать в параметре ссылку на хранилище ключей и секрет. Это значение никогда не будет раскрыто, так как указывается только его идентификатор в хранилище ключей. Вам не нужно будет вручную вводить значение секрета при каждом развертывании ресурсов. Хранилище ключей не обязательно должно находится в той же подписке, что и группа ресурсов, развертывание в которую выполняется. При указании ссылки на хранилище ключей можно добавить идентификатор подписки.

В этом разделе показано, как создать хранилище ключей и секрет, настроить доступ к секрету для шаблона Resource Manager и передать секрет в качестве параметра. Если у вас уже есть хранилище ключей и секрет, но вам нужно проверить шаблон и доступ пользователей, перейдите к разделу [Разрешение доступа к секрету](#enable-access-to-the-secret). Если у вас уже есть хранилище ключей и секрет и вы уверены, что они настроены для шаблона и доступа пользователей, перейдите к разделу [Ссылка на секретный код со статическим идентификатором](#reference-a-secret-with-static-id). 

## <a name="deploy-a-key-vault-and-secret"></a>Развертывание хранилища ключей и секретного кода

Можно развернуть хранилище ключей и секрет с помощью шаблона Resource Manager. Пример см. в разделах [Шаблон хранилища ключей](resource-manager-template-keyvault.md) и [Шаблон секрета хранилища ключей](resource-manager-template-keyvault-secret.md). При создании хранилища ключей задайте для свойства **enabledForTemplateDeployment** значение **true**, чтобы ссылки на это хранилище можно было использовать в других шаблонах Resource Manager. 

Можно также создать хранилище ключей и секрет с помощью портала Azure. 

1. Щелкните **Создать** -> **Безопасность+идентификация** -> **Хранилище ключей**.

   ![Создание хранилища ключей](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Укажите значения для хранилища ключей. Параметры **Политики доступа** и **Политика расширенного доступа** на данном этапе можно пропустить. Они описаны далее в разделе. Нажмите кнопку **Создать**.

   ![Настройка хранилища ключей](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. Теперь у вас есть хранилище ключей. Выберите его.

4. В колонке хранилища ключей выберите **Секреты**.

   ![Выбор секретов](./media/resource-manager-keyvault-parameter/select-secret.png)

5. Выберите **Добавить**.

   ![выбрать "добавить"](./media/resource-manager-keyvault-parameter/add-secret.png)

6. Выберите параметр передачи **Вручную**. Укажите имя и значение для секрета. Нажмите кнопку **Создать**.

   ![Настройка секрета](./media/resource-manager-keyvault-parameter/provide-secret.png)

Вы создали хранилище ключей и секрет.

## <a name="enable-access-to-the-secret"></a>Разрешение доступа к секрету

При использовании как нового, так и существующего хранилища ключей убедитесь, что у пользователя, который развертывает шаблон, есть доступ к секрету. Пользователь, развертывающий шаблон, который ссылается на секрет, должен иметь разрешение `Microsoft.KeyVault/vaults/deploy/action` для хранилища ключей. Оно имеется у ролей [Владелец](../active-directory/role-based-access-built-in-roles.md#owner) и [Участник](../active-directory/role-based-access-built-in-roles.md#contributor). Можно также создать [пользовательскую роль](../active-directory/role-based-access-control-custom-roles.md), которая предоставляет данное разрешение, и добавить пользователя в эту роль. Кроме того, Resource Manager необходимо предоставить доступ к хранилищу ключей во время развертывания.

Проверить наличие доступа или настроить его можно на портале.

1. Выберите **Управление доступом (IAM)**.

   ![Выбор управления доступом](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. Если учетной записи, которая будет использоваться для развертывания шаблонов, не назначена роль владельца или участника (или пользовательская роль с разрешением `Microsoft.KeyVault/vaults/deploy/action`), то выберите **Добавить**.

   ![добавить пользователя](./media/resource-manager-keyvault-parameter/add-user.png)

3. Выберите роль участника или владельца и найдите удостоверение, чтобы назначить его этой роли. Щелкните **ОК**, чтобы завершить добавление удостоверения для роли.

   ![добавить пользователя](./media/resource-manager-keyvault-parameter/search-user.png)

4. Чтобы включить доступ для шаблона во время развертывания, выберите **Политика расширенного доступа**. Выберите параметр **Включить доступ к Azure Resource Manager для развертывания шаблонов**.

   ![Включение доступа для шаблона](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>Ссылка на секретный код со статическим идентификатором

Ссылка на секрет добавляется в **файл параметров (а не в шаблон)**, который передает значения в шаблон. Для ссылки на секретный код необходимо передать идентификатор ресурса хранилища ключей и имя секретного кода. В приведенном ниже примере секрет хранилища ключей уже должен существовать. При этом для идентификатора ресурса используется статическое значение.

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

Принимать секрет в шаблоне должен параметр **securestring**. В следующем примере показаны соответствующие разделы шаблона, который развертывает SQL Server и требует пароля администратора.

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
* Полные примеры использования ссылок на секреты ключей приведены [здесь](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).



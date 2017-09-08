---
title: "Создание и публикация управляемого приложения каталога услуг Azure | Документация Майкрософт"
description: "Показано, как создать управляемое приложение Azure, предназначенное для членов вашей организации."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 39b74984ec2f89ed39753963de7fe3ff79577c9e
ms.contentlocale: ru-ru
ms.lasthandoff: 08/25/2017

---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Публикация управляемого приложения для внутреннего использования

Вы можете создавать и публиковать [управляемые приложения Azure](managed-application-overview.md), предназначенные для членов вашей организации. Например, отдел ИТ может публиковать управляемые приложения, которые обеспечивают соответствие стандартам организации. Эти управляемые приложения доступны через каталог услуг, а не Azure Marketplace.

Чтобы опубликовать управляемое приложение для каталога услуг, необходимо выполнить следующее.

* Создайте ZIP-файл пакета, содержащий три необходимых файла шаблонов.
* Определите, каким пользователям, группам или приложениям нужен доступ к группе ресурсов в подписке пользователя.
* Создайте определение управляемого приложения, которое указывает на ZIP-файл пакета и запрашивает доступ для получения удостоверения.

## <a name="create-a-managed-application-package"></a>Создание пакета управляемого приложения

Первым шагом является создание трех необходимых файлов шаблонов. Упакуйте все три файла в ZIP-файл и передайте его в доступное расположение, например в учетную запись хранения. Ссылка на этот ZIP-файл передается при создании определения управляемого приложения.

* **applianceMainTemplate.json**. Этот файл шаблона определяет ресурсы Azure, подготовленные как часть управляемого приложения. Этот шаблон ничем не отличается от обычного шаблона Resource Manager. Например, чтобы создать учетную запись хранения с помощью управляемого приложения, в файле applianceMainTemplate.json можно определить следующие параметры.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **mainTemplate.json**. Пользователи развертывают этот шаблон при создании управляемого приложения. Он определяет ресурс управляемого приложения, который является ресурсом типа Microsoft.Solutions/appliances. В этом файле указаны все параметры, необходимые для ресурсов в файле applianceMainTemplate.json.

  В этом шаблоне задаются два важных свойства. Первое свойство, **applianceDefinitionId**, — это идентификатор определения управляемого приложения. Определение будет создано далее в этом разделе. При настройке этого параметра необходимо решить, какую подписку и группу ресурсов использовать для хранения определений управляемых приложений. И необходимо выбрать имя определения. Идентификатор имеет следующий формат.

  `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Solutions/applianceDefinitions/<definition-name>`

  Второе свойство, **managedResourceGroupId** — это идентификатор группы ресурсов, в которой создаются ресурсы Azure. Можно присвоить значение имени этой группы ресурсов или позволить пользователю указать ее имя. Идентификатор имеет следующий формат.

  `/subscriptions/<subscription-id>/resourceGroups/<resoure-group-name>`.

  В следующем примере показан файл mainTemplate.json. Он задает группу ресурсов для развернутых ресурсов. Задан идентификатор определения для использования определения **storageApp** в группе ресурсов **managedApplicationGroup**. Можно изменить эти значения, указав другие имена. Укажите собственный идентификатор подписки в идентификаторе определения.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "variables": {
        "managedRGId": "[concat(resourceGroup().id,'-application-resources')]",
        "managedAppName": "[concat('managedStorage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/appliances",
            "name": "[variables('managedAppName')]",
            "apiVersion": "2016-09-01-preview",
            "location": "[resourceGroup().location]",
            "kind": "ServiceCatalog",
            "properties": {
                "managedResourceGroupId": "[variables('managedRGId')]",
                "applianceDefinitionId": "/subscriptions/<subscription-id>/resourceGroups/managedApplicationGroup/providers/Microsoft.Solutions/applianceDefinitions/storageApp",
                "parameters": {
                    "storageAccountNamePrefix": {
                        "value": "[parameters('storageAccountNamePrefix')]"
                    }
                }
            }
        }
    ]
  }
  ```

* **applianceCreateUiDefinition.json**. На основе этого файла портал Azure создает пользовательский интерфейс для пользователей, создающих управляемое приложение. Можно определить способ ввода каждого параметра пользователями. например раскрывающийся список, текстовое поле, поле ввода пароля или другие средства ввода. Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).

  В следующем примере показан файл applianceCreateUiDefinition.json, который позволяет пользователям указать префикс имени учетной записи хранения в текстовом поле.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

Подготовив все необходимые файлы, упакуйте их в ZIP-файл. Приведенные выше три файла должны быть расположены на корневом уровне ZIP-файла. Если поместить их в папку, то при создании определения управляемого приложения появится сообщение об ошибке из-за отсутствия необходимых файлов. Передайте пакет в доступное расположение, из которого его можно будет использовать. В оставшейся части этой статьи предполагается, что ZIP-файл существует в контейнере больших двоичных объектов в общедоступном хранилище.

## <a name="create-an-azure-active-directory-user-group-or-application"></a>Создание группы пользователей или приложения Azure Active Directory

Второй шаг — выбор группы пользователей или приложения для управления ресурсами от имени клиента. Этой группе ресурсов или приложению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Это может быть любая встроенная роль управления доступом на основе ролей (RBAC), например "Владелец" или "Участник". Можно также предоставить разрешение на управление ресурсами отдельному пользователю, но обычно это разрешение назначается группе пользователей. Чтобы создать группу пользователей Active Directory, ознакомьтесь с разделом [Создание группы и добавление в нее пользователей в Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Требуется идентификатор объекта группы пользователей для управления ресурсами. Пример ниже позволяет получить идентификатор объекта по отображаемому имени группы.

```azurecli-interactive
az ad group show --group exampleGroupName
```

Пример команды возвращает следующие выходные данные.

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

Чтобы получить только идентификатор объекта, используйте следующую команду.

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

## <a name="get-the-role-definition-id"></a>Получение идентификатора определения роли

Далее необходимо получить идентификатор определения встроенной роли RBAC, к которой вы хотите предоставить доступ пользователю, группе пользователей или приложению. Обычно это роль "Владелец", "Участник" или "Читатель". Следующая команда позволяет получить идентификатор определения роли "Владелец".


```azurecli-interactive
az role definition list --name owner
```

Эта команда возвращает следующие выходные данные.

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
         ],
         "notActions": []
        }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

Вам требуется значение свойства name из предыдущего примера. Вы можете получить только это свойство, выполнив следующую команду.

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

## <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

Если у вас еще нет группы ресурсов для хранения определения управляемого приложения, создайте ее.

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

Теперь создайте ресурс определения управляемого приложения.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

В предыдущем примере использовались следующие параметры:

* **resource-group**. Имя группы ресурсов, в которой создается определение управляемого приложения.
* **lock-level**. Тип блокировки управляемой группы ресурсов. Этот параметр предотвращает выполнение нежелательных операций в группе ресурсов. Сейчас поддерживается только тип блокировки ReadOnly. Если указать этот тип, пользователь будет иметь только доступ на чтение к ресурсам в управляемой группе ресурсов.
* **authorizations**. Содержит идентификатор субъекта и идентификатор определения роли, используемые для предоставления разрешения для управляемой группы ресурсов. Это свойство указывается в формате `<principalId>:<roleDefinitionId>`. Для него можно указать несколько значений. Если нужно указать несколько значений, то их необходимо указать в следующем формате: `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Разные значения разделяются пробелами.
* **package-file-uri**. Расположение пакета управляемого приложения, содержащего файлы шаблонов. Это может быть большой двоичный объект службы хранения Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в разделе [Обзор управляемых приложений Azure](managed-application-overview.md).
* Примеры файлов см. в репозитории с [управляемыми приложениями](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Дополнительные сведения об использовании управляемых приложений из каталога услуг см. в разделе [Использование управляемого приложения Azure](managed-application-consumption.md).
* Дополнительные сведения о публикации управляемых приложений в Azure Marketplace см. в статье [Управляемые приложения Azure в Marketplace](managed-application-author-marketplace.md).
* Сведения об использовании управляемого приложения из Marketplace см. в статье [Использование управляемых приложений Azure в Marketplace](managed-application-consume-marketplace.md).
* Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).


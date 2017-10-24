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
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Публикация управляемого приложения для внутреннего использования

Вы можете создавать и публиковать [управляемые приложения Azure](managed-application-overview.md), предназначенные для членов вашей организации. Например, отдел ИТ может публиковать управляемые приложения, которые обеспечивают соответствие стандартам организации. Эти управляемые приложения доступны через каталог услуг, а не Azure Marketplace.

Чтобы опубликовать управляемое приложение для каталога услуг, необходимо выполнить следующее.

* Создайте ZIP-файл пакета, содержащий два необходимых файла шаблонов.
* Определите, каким пользователям, группам или приложениям нужен доступ к группе ресурсов в подписке пользователя.
* Создайте определение управляемого приложения, которое указывает на ZIP-файл пакета и запрашивает доступ для получения удостоверения.

## <a name="create-a-managed-application-package"></a>Создание пакета управляемого приложения

Первым шагом является создание двух необходимых файлов шаблонов. Упакуйте оба файла в ZIP-файл и передайте его в доступное расположение, например в учетную запись хранения. Ссылка на этот ZIP-файл передается при создании определения управляемого приложения.

* **mainTemplate.json**. Этот файл шаблона определяет ресурсы Azure, подготовленные как часть управляемого приложения. Этот шаблон ничем не отличается от обычного шаблона Resource Manager. Например, чтобы создать учетную запись хранения с помощью управляемого приложения, в файле mainTemplate.json можно определить следующие параметры:

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

* **createUiDefinition.json**. На основе этого файла портал Azure создает пользовательский интерфейс для пользователей, создающих управляемое приложение. Можно определить способ ввода каждого параметра пользователями. например раскрывающийся список, текстовое поле, поле ввода пароля или другие средства ввода. Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).

  В следующем примере показан файл createUiDefinition.json, который позволяет пользователям указать префикс имени учетной записи хранения в текстовом поле.

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

Подготовив все необходимые файлы, упакуйте их в ZIP-файл. Приведенные выше два файла должны быть расположены на корневом уровне ZIP-файла. Если поместить их в папку, то при создании определения управляемого приложения появится сообщение об ошибке из-за отсутствия необходимых файлов. Передайте пакет в доступное расположение, из которого его можно будет использовать. В оставшейся части этой статьи предполагается, что ZIP-файл существует в контейнере больших двоичных объектов в общедоступном хранилище.

Чтобы создать управляемое приложение для каталога услуг, можно использовать Azure CLI или портал. В этой статье описаны оба подхода.

## <a name="create-managed-application-with-azure-cli"></a>Создание управляемого приложения с помощью Azure CLI

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Создание группы пользователей или приложения Azure Active Directory

Следующий шаг — выбор группы пользователей или приложения для управления ресурсами от имени клиента. Этой группе ресурсов или приложению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Это может быть любая встроенная роль управления доступом на основе ролей (RBAC), например "Владелец" или "Участник". Можно также предоставить разрешение на управление ресурсами отдельному пользователю, но обычно это разрешение назначается группе пользователей. Чтобы создать группу пользователей Active Directory, ознакомьтесь с разделом [Создание группы и добавление в нее пользователей в Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

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

### <a name="get-the-role-definition-id"></a>Получение идентификатора определения роли

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

### <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

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

## <a name="create-managed-application-with-portal"></a>Создание управляемого приложения с помощью портала

1. При необходимости создайте группу пользователей Azure Active Directory для управления ресурсами. Дополнительные сведения см. в статье [Создание группы и добавление в нее пользователей в Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).
1. В левом верхнем углу выберите **+Создать**.

   ![Новая служба](./media/managed-application-publishing/new.png)

1. Выполните поиск словосочетания **каталог услуг**.

1. В результатах найдите **Определение управляемого приложения каталога услуг**. Выберите его.

   ![Поиск определений управляемых приложений](./media/managed-application-publishing/select-managed-apps-definition.png)

1. Выберите **Создать**, чтобы создать определение управляемого приложения.

   ![Создание определения управляемого приложения](./media/managed-application-publishing/create-definition.png)

1. Укажите значения имени, отображаемого имени, описания, расположения, подписки и группы ресурсов. Для URI файла пакета укажите путь к созданному вами ZIP-файлу.

   ![Определение значений](./media/managed-application-publishing/fill-application-values.png)

1. Когда откроется раздел "Аутентификация и уровень блокировки", выберите **Добавить авторизацию**.

   ![Добавление авторизации](./media/managed-application-publishing/add-authorization.png)

1. Выберите группу пользователей Azure Active Directory для управления ресурсами и нажмите **ОК**.

   ![Добавление группы авторизации](./media/managed-application-publishing/add-auth-group.png)

1. Указав все значения, выберите **Создать**.

   ![Создание управляемого приложения](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в разделе [Обзор управляемых приложений Azure](managed-application-overview.md).
* Примеры файлов см. в репозитории с [управляемыми приложениями](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Дополнительные сведения об использовании управляемых приложений из каталога услуг см. в разделе [Использование управляемого приложения Azure](managed-application-consumption.md).
* Дополнительные сведения о публикации управляемых приложений в Azure Marketplace см. в статье [Управляемые приложения Azure в Marketplace](managed-application-author-marketplace.md).
* Сведения об использовании управляемого приложения из Marketplace см. в статье [Использование управляемых приложений Azure в Marketplace](managed-application-consume-marketplace.md).
* Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).

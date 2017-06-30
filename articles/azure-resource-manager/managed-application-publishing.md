---
title: "Создание и публикация управляемого приложения Azure | Документация Майкрософт"
description: "Сведения о создании управляемого приложения Azure независимыми поставщиками программных продуктов и партнерами."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Создание и публикация управляемого приложения Azure 

Как описано в статье [Обзор управляемых приложений Azure](managed-application-overview.md), существует два сценария работы с управляемыми приложениями: первый — для издателя или независимого поставщика программного обеспечения, желающего создать управляемое приложение и предоставить его пользователям, и второй — для пользователя, использующего управляемое приложение. В этой статье рассматривается первый сценарий и объясняется, как независимый поставщик программного обеспечения может создать и опубликовать управляемое приложение. 

Чтобы создать управляемое приложение, вам потребуется:

* пакет с файлами шаблона;
* пользователь, группа или приложение с доступом к группе ресурсов в подписке клиента;
* определение устройства.

Примеры файлов см. в репозитории с [управляемыми приложениями](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).

## <a name="create-managed-application-package"></a>Создание пакета управляемого приложения

Сначала нужно создать пакет управляемого приложения, в котором содержатся файлы основного шаблона. Издатель или независимый поставщик программного обеспечения создает три файла. 

* Первый файл — **applianceMainTemplate.json**. Он определяет фактические ресурсы, подготовленные как часть управляемого приложения. Например, чтобы создать учетную запись хранения с помощью управляемого приложения, в файле applianceMainTemplate.json можно определить следующие параметры: 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* Второй файл, необходимый издателю, называется **mainTemplate.json**. В этом файле шаблона указаны только ресурсы устройства (Microsoft.Solutions/appliances). Здесь также содержатся все параметры, необходимые для ресурсов в файле applianceMainTemplate.json. 

  При создании управляемого приложения вам потребуются два важных свойства. Свойство **managedResourceGroupId** — это идентификатор группы ресурсов, где создаются ресурсы, определенные в файле applianceMainTemplate.json. Он имеет следующий формат: `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`.

  Свойство **applianceDefinitionId** — это идентификатор определения ресурса управляемого приложения. Он имеет следующий формат: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`.

  Задайте параметры для этих двух свойств, чтобы пользователи смогли их указать при создании управляемого приложения. В примере ниже этим двум свойствам соответствуют параметры managedByResourceGroup и applianceDefinitonId.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* Третий файл, который нужно добавить в пакет, — **createUiDefinition.json**. На основе этого файла портал Azure создает пользовательский интерфейс управляемого приложения. Вы можете определить параметры управляемого приложения и способ их ввода, например раскрывающийся список, текстовое поле или другое средство ввода. Дополнительные сведения о создании файла определения пользовательского интерфейса для управляемого приложения см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).

После подготовки всех необходимых файлов отправьте пакет в доступное расположение, из которого его можно будет извлечь.


## <a name="create-azure-ad-user-group-or-application"></a>Создание приложения или группы пользователей Azure AD
Затем создайте приложение или группу пользователей. Их можно использовать, чтобы управлять ресурсами от имени пользователя. Этой группе ресурсов или приложению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Это может быть любая встроенная роль RBAC, например **Владелец** или **Участник**. Отдельному пользователю можно также предоставить разрешения на управление ресурсами, но обычно это право предоставляется группе пользователей. Чтобы создать группу пользователей Active Directory, выполните следующую команду:

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

Вы также можете использовать имеющуюся группу. Теперь нужно получить идентификатор объекта созданной или имеющейся группы пользователей. Пример команды ниже позволяет получить идентификатор объекта из отображаемого имени, используемого для создания группы.

```azurecli
az ad group show --group "groupName"
```

Пример:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

Эта команда возвращает следующие выходные данные:

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
Вам потребуется значение objectId из примера выше. 

## <a name="get-the-role-definition-id"></a>Получение идентификатора определения роли

Далее необходимо получить идентификатор определения встроенной роли RBAC, к которой вы хотите предоставить доступ пользователю, группе пользователей или приложению. Обычно это роль "Владелец", "Участник" или "Читатель". Следующая команда позволяет получить идентификатор определения роли для роли "Владелец":

```azurecli
az role definition list --name owner
```

Эта команда возвращает следующие выходные данные:

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

Вам потребуется значение свойства "name" из предыдущего примера.


## <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

Сначала нужно создать ресурс определения управляемого приложения. После создания пакета устройства и добавления разрешений создайте определение устройства, выполнив следующую команду: 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

В предыдущем примере использовались следующие параметры:

- resource-group. Имя группы ресурсов, в которой создается определение устройства.
- lock-level. Тип блокировки управляемой группы ресурсов. Этот параметр предотвращает выполнение нежелательных операций в группе ресурсов. Сейчас поддерживается только тип блокировки **ReadOnly**. Если указать этот тип, пользователь будет иметь только доступ на чтение к ресурсам в управляемой группе ресурсов.
- authorizations. Содержит идентификатор субъекта-службы и идентификатор определения роли, используемые для предоставления разрешений в управляемой группе ресурсов. Этот параметр указывается в формате `<principalId>:<roleDefinitionId>`. Для него можно указать несколько значений. В этом случае их необходимо указать в следующем формате: `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Разные значения разделяются пробелами.
- package-file-uri. Расположение пакета устройства, в котором содержатся файлы шаблона. Этот пакет можно хранить в Azure Storage Blob. 


## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* Примеры файлов см. в репозитории с [управляемыми приложениями](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* См. сведения об [использовании управляемого приложения Azure](managed-application-consumption.md) для пользователя.
* Дополнительные сведения о создании файла определения пользовательского интерфейса для управляемого приложения см. в статье [Начало работы с CreateUiDefinition](managed-application-createuidefinition-overview.md).


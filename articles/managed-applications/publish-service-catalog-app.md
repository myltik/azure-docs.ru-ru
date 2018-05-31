---
title: Создание и публикация управляемого приложения каталога услуг Azure | Документация Майкрософт
description: Показано, как создать управляемое приложение Azure, предназначенное для членов вашей организации.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: b7f8bbcad39000e7e71149824535a6a82b26c758
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305316"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Публикация управляемого приложения для внутреннего использования

Вы можете создавать и публиковать [управляемые приложения Azure](overview.md), предназначенные для членов вашей организации. Например, отдел ИТ может публиковать управляемые приложения, которые обеспечивают соответствие стандартам организации. Эти управляемые приложения доступны в каталоге служб, а не в Azure Marketplace.

Чтобы опубликовать управляемое приложение для каталога услуг, необходимо выполнить следующее.

* Создайте шаблон, который определяет ресурсы, развертываемые с управляемым приложением.
* Определите элементы пользовательского интерфейса для портала при развертывании управляемого приложения.
* Создайте ZIP-файл пакета, содержащий необходимые файлы шаблонов.
* Определите, каким пользователям, группам или приложениям нужен доступ к группе ресурсов в подписке пользователя.
* Создайте определение управляемого приложения, которое указывает на ZIP-файл пакета и запрашивает доступ для получения удостоверения.

Используемое в этой статье управляемое приложение содержит только учетную запись хранения. В этом руководстве мы покажем, как опубликовать управляемое приложение. Полные примеры приведены в статье [Примеры проектов для управляемых приложений Azure](sample-projects.md).

## <a name="create-the-resource-template"></a>Создание шаблона ресурсов

В каждом определении управляемого приложения указан файл с именем **mainTemplate.json**. В нем определяются ресурсы Azure, которые необходимо подготовить. Этот шаблон ничем не отличается от обычного шаблона Resource Manager.

Создайте файл с именем **mainTemplate.json**. В имени следует учитывать регистр.

Добавьте в файл приведенный ниже код JSON. В нем определяются параметры для создания учетной записи хранения и задаются свойства для нее.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Сохраните файл mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Создание определения пользовательского интерфейса

На основе файла **createUiDefinition.json** портал Azure создает пользовательский интерфейс для пользователей, создающих управляемое приложение. Можно определить способ ввода каждого параметра пользователями. например раскрывающийся список, текстовое поле, поле ввода пароля или другие средства ввода. Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).

Создайте файл с именем **createUiDefinition.json**. В имени следует учитывать регистр.

Добавьте в файл следующий код JSON.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Сохраните файл createUiDefinition.json.

## <a name="package-the-files"></a>Упаковка файлов

Добавьте два файла в ZIP-файл с именем app.zip. Приведенные выше два файла должны быть расположены на корневом уровне ZIP-файла. Если поместить их в папку, то при создании определения управляемого приложения появится сообщение об ошибке из-за отсутствия необходимых файлов. 

Передайте пакет в доступное расположение, из которого его можно будет использовать. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Создание группы пользователей или приложения Azure Active Directory

Следующий шаг — выбор группы пользователей или приложения для управления ресурсами от имени клиента. Этой группе ресурсов или приложению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Это может быть любая встроенная роль управления доступом на основе ролей (RBAC), например "Владелец" или "Участник". Можно также предоставить разрешение на управление ресурсами отдельному пользователю, но обычно это разрешение назначается группе пользователей. Чтобы создать группу пользователей Active Directory, ознакомьтесь с разделом [Создание группы и добавление в нее пользователей в Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Требуется идентификатор объекта группы пользователей для управления ресурсами. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Получение идентификатора определения роли

Далее необходимо получить идентификатор определения встроенной роли RBAC, к которой вы хотите предоставить доступ пользователю, группе пользователей или приложению. Обычно это роль "Владелец", "Участник" или "Читатель". Следующая команда позволяет получить идентификатор определения роли "Владелец".

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

Если у вас еще нет группы ресурсов для хранения определения управляемого приложения, создайте ее.

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Теперь создайте ресурс определения управляемого приложения.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application"></a>Создание управляемого приложения

Управляемое приложение можно развернуть с помощью портала, PowerShell или Azure CLI.

### <a name="powershell"></a>PowerShell

Для начала давайте развернем управляемое приложение с помощью PowerShell.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

Теперь управляемое приложение и управляемая инфраструктура существуют в подписке.

### <a name="portal"></a>Microsoft Azure

Теперь давайте развернем управляемое приложение с помощью портала. Вы увидите пользовательский интерфейс, созданный в пакете.

1. Перейдите на портал Azure. Выберите **+ Создать ресурс** и выполните поиск по фразе **каталог услуг**.

   ![Поиск каталога услуг](./media/publish-service-catalog-app/create-new.png)

1. Выберите **Управляемое приложение каталога услуг**.

   ![Выбор каталога услуг](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Нажмите кнопку **Создать**.

   ![Выбор кнопки "Создать"](./media/publish-service-catalog-app/select-create.png)

1. Найдите управляемое приложение, которое необходимо создать, в списке доступных решений и выберите его. Нажмите кнопку **Создать**.

   ![Поиск управляемого приложения](./media/publish-service-catalog-app/find-application.png)

1. Укажите основные сведения, необходимые для создания управляемого приложения. Укажите подписку и новую группу ресурсов, к которой будет относиться управляемое приложение. Выберите расположение **Западно-центральная часть США**. Когда все будет готово, нажмите кнопку **ОК**.

   ![Выбор параметров управляемого приложения](./media/publish-service-catalog-app/add-basics.png)

1. Укажите значения для ресурсов в управляемом приложении. Когда все будет готово, нажмите кнопку **ОК**.

   ![Выбор параметров ресурсов](./media/publish-service-catalog-app/add-storage-settings.png)

1. Шаблон проверяет введенные значения. Если проверка прошла успешно, нажмите кнопку **ОК**, чтобы запустить развертывание.

   ![Проверка управляемого приложения](./media/publish-service-catalog-app/view-summary.png)

По завершении развертывания управляемое приложение появится в группе ресурсов applicationGroup. Учетная запись хранения появится в группе ресурсов applicationGroup вместе с хэшированным строковым значением.

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в разделе [Обзор управляемых приложений Azure](overview.md).
* Примеры проектов см. в статье [Sample projects for Azure managed applications](sample-projects.md) (Примеры проектов для управляемых приложений Azure).
* Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).

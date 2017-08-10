---
title: "Создание первого шаблона Azure Resource Manager | Документация Майкрософт"
description: "Пошаговые инструкции по созданию первого шаблона Azure Resource Manager. Здесь объясняется, как создать шаблон, указав в нем ссылку на учетную запись хранения."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: ru-ru
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Создание и развертывание первого шаблона Azure Resource Manager
В этой статье рассматриваются действия по созданию первого шаблона Azure Resource Manager. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md). Если вы уже развернули ресурсы и хотите получить для них шаблон, см. статью [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).

Для создания и изменения шаблонов нужен редактор JSON, например [Visual Studio Code](https://code.visualstudio.com/). Это упрощенный кроссплатформенный редактор с открытым исходным кодом. Для создания шаблонов Resource Manager мы настоятельно рекомендуем использовать Visual Studio Code. В этой статье предполагается, что вы используете VS Code. Но вы можете использовать и другой редактор JSON (например, Visual Studio).

## <a name="prerequisites"></a>Предварительные требования

* Visual Studio Code. При необходимости установите его со страницы [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-template"></a>Создание шаблона

Давайте начнем с простого шаблона, который развертывает учетную запись хранения в подписке.

1. Выберите **Файл** > **Создать файл**. 

   ![Создание файла](./media/resource-manager-create-first-template/new-file.png)

2. Скопируйте и вставьте в него следующий синтаксис JSON:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Для имен учетных записей хранения существует несколько ограничений, которые усложняют настройку имен. Имя должно содержать от 3 до 24 символов, состоять только из цифр и букв нижнего регистра и быть уникальным. Для создания значения хэша предыдущий шаблон использует функцию [uniqueString](resource-group-template-functions-string.md#uniquestring). Чтобы присвоить этому значению хэша дополнительное значение, он добавляет префикс *storage*. 

3. Сохраните этот файл как **azuredeploy.json** в локальной папке.

   ![Сохранение шаблона](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Развертывание шаблона

Теперь вы можете развернуть этот шаблон. Чтобы создать группу ресурсов, вы можете использовать PowerShell или Azure CLI. Затем можно развернуть учетную запись хранения в этой группе ресурсов.

* Для PowerShell используйте следующие команды из папки, содержащей шаблон:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Для локальной установки Azure CLI используйте следующие команды из папки, содержащей шаблон:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

После завершения развертывания учетная запись хранения будет находиться в группе ресурсов.

## <a name="deploy-template-from-cloud-shell"></a>Развертывание шаблона из Cloud Shell

Вы можете использовать [Cloud Shell](../cloud-shell/overview.md) для выполнения команд Azure CLI для развертывания шаблона. Однако сначала необходимо загрузить шаблон в общий файловый ресурс для Cloud Shell. Если вы еще не использовали Cloud Shell, ознакомьтесь со статьей [Обзор Azure Cloud Shell (предварительная версия)](../cloud-shell/overview.md), чтобы узнать о настройке службы.

1. Войдите на [портал Azure](https://portal.azure.com).   

2. Выберите свою группу ресурсов Cloud Shell. Шаблон имени — `cloud-shell-storage-<region>`.

   ![Выбор группы ресурсов](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Выберите учетную запись хранения для Cloud Shell.

   ![Выбор учетной записи хранения](./media/resource-manager-create-first-template/select-storage.png)

4. Выберите **Файлы**.

   ![Выбор файлов](./media/resource-manager-create-first-template/select-files.png)

5. Выберите общий файловый ресурс для Cloud Shell. Шаблон имени — `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Выбор общего файлового ресурса](./media/resource-manager-create-first-template/select-file-share.png)

6. Выберите **Добавить каталог**.

   ![Добавление каталога](./media/resource-manager-create-first-template/select-add-directory.png)

7. Назовите его **templates** и нажмите кнопку **ОК**.

   ![Присвоение имени каталогу](./media/resource-manager-create-first-template/name-templates.png)

8. Выберите новый каталог.

   ![Выбор каталога](./media/resource-manager-create-first-template/select-templates.png)

9. Щелкните **Отправить**.

   ![Кнопка "Отправить"](./media/resource-manager-create-first-template/select-upload.png)

10. Найдите и отправьте свой шаблон.

   ![Отправка файла](./media/resource-manager-create-first-template/upload-files.png)

11. Откройте командную строку.

   ![Открытие Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. В Cloud Shell введите следующие команды:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
   ```

После завершения развертывания учетная запись хранения будет находиться в группе ресурсов.

## <a name="customize-the-template"></a>Настройка шаблона

Шаблон работает, как и полагается, но не обладает гибкостью. Он всегда развертывает локально избыточное хранилище в юго-центральный регион США. Именем всегда является *storage*, за которым следует значение хэша. Чтобы использовать шаблон для различных сценариев, добавьте к нему параметры.

В примере ниже показан раздел параметров с двумя параметрами. Первый параметр, `storageSKU`, позволяет указать тип избыточности. Он ограничивает значения, которые можно передать в значения, допустимые для учетной записи хранения. Он также задает значение по умолчанию. Второй параметр, `storageNamePrefix`, допускает максимум 11 знаков. Он задает значение по умолчанию.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

В разделе переменных добавьте переменную с именем `storageName`. Она объединяет значение префикса из параметров и значение хэша из функции [uniqueString](resource-group-template-functions-string.md#uniquestring). Эта переменная использует функцию [toLower](resource-group-template-functions-string.md#tolower) для преобразования всех знаков в нижний регистр.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Чтобы использовать эти новые значения для учетной записи хранения, измените определение ресурса:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Обратите внимание, что имени учетной записи хранения теперь присвоена добавленная переменная. Имени номера SKU присваивается значение параметра. Расположению присваивается то же расположение, что и группе ресурсов.

Сохраните файл. 

После завершения действий, описанных в этой статье, шаблон будет выглядеть так:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Повторное развертывание шаблона

Разверните повторно шаблон с другими значениями.

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Для интерфейса командной строки Azure:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Для Cloud Shell отправьте измененный шаблон в общий файловый ресурс. Перезапишите существующий файл. Затем используйте следующую команду:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если развернутые ресурсы вам больше не нужны, вы можете их удалить. Для этого удалите группу ресурсов.

Для PowerShell используйте команду:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Для интерфейса командной строки Azure:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения о свойствах учетной записи хранения см. в статье [Microsoft.Storage/storageAccounts template reference](/azure/templates/microsoft.storage/storageaccounts) (Справочник по шаблону Microsoft.Storage/storageAccounts).
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).


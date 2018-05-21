---
title: Создание шаблона Azure Resource Manager с помощью расширения VS Code | Документация Майкрософт
description: Используйте расширение "Средства Azure Resource Manager" для работы с шаблонами Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: f05b0baee3f11f498976377c69c38b3118f3c922
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager c помощью расширения Visual Studio Code
В этой статье описаны преимущества установки и использования расширения "Средства Azure Resource Manager" в Visual Studio Code. Шаблоны Resource Manager в VS Code можно создавать и без расширения. Но расширение предоставляет варианты автозаполнения, которые упрощают разработку шаблона. Расширение предлагает функции шаблона, параметры и переменные, доступные в шаблоне.

Для работы с этой статьей требуется [Visual Studio Code](https://code.visualstudio.com/).

Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md).

## <a name="create-the-template"></a>Создание шаблона

В этой статье используется шаблон, созданный при работе с руководством [Создание и развертывание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md). Если у вас есть такой шаблон, можете пропустить этот раздел.

1. Чтобы создать шаблон, запустите VS Code. Выберите **Файл** > **Создать файл**. 

   ![Создание файла](./media/resource-manager-vscode-extension/new-file.png)

2. Скопируйте и вставьте в него следующий синтаксис JSON:

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
         "properties": {
           "encryption":{
             "services":{
               "blob":{
                 "enabled":true
               }
             },
             "keySource":"Microsoft.Storage"
           }
         }
       }
     ],
     "outputs": {  }
   }
   ```

3. Сохраните этот файл как **azuredeploy.json** в локальной папке.

   ![Сохранение шаблона](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Установка расширения

1. В VS Code выберите **Расширения**.

   ![Выбор меню "Расширения"](./media/resource-manager-vscode-extension/select-extensions.png)

2. Выполните поиск по фразе **Средства Azure Resource Manager** и нажмите кнопку **Установить**.

   ![Установка расширения](./media/resource-manager-vscode-extension/install-extension.png)

3. Чтобы завершить установку расширения, щелкните **Перезагрузить**.

## <a name="edit-the-template"></a>Изменение шаблона

1. Откройте файл azuredeploy.json.

2. Расширение извлекает все доступные [функции шаблона](resource-group-template-functions.md). Кроме того, оно считывает параметры и переменные, определенные в шаблоне. Чтобы узнать, как работают эти функции, добавьте два значения в раздел выходных данных. В шаблоне замените раздел выходных данных следующим:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Поместите курсор внутри кавычек для значения в строке **groupLocation**. Введите открывающую квадратную скобку (`[`). Обратите внимание, что расширение сразу же предлагает доступные функции шаблона.

   ![Отображение доступных функций](./media/resource-manager-vscode-extension/available-functions.png)

4. Начните вводить **resourceGroup**. Когда функция `resourceGroup()` отобразится, нажмите клавишу TAB или ВВОД.

   ![Выбор функции resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. Расширение заполняет синтаксис функции. Функция [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) не принимает параметры. Поставьте точку после закрывающей скобки. Расширение предоставит свойства, доступные для объекта, возвращаемого функцией `resourceGroup()`. Выберите `location`.

   ![Выбор свойств](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. После **location** добавьте закрывающую квадратную скобку.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. Теперь поместите курсор внутри кавычек в строке **storageUri**. Снова введите открывающую скобку. Начните вводить **reference**. Выбрав эту функцию, нажмите клавишу TAB или ВВОД.

   ![Выбор функции reference](./media/resource-manager-vscode-extension/add-reference.png)

8. Функция [reference](resource-group-template-functions-resource.md#reference) принимает в качестве параметра идентификатор ресурса или имя ресурса. Ранее вы задали имя учетной записи хранения в переменной. Введите **var**, а затем нажмите сочетание клавиш CTRL+ПРОБЕЛ. Расширение предложит функцию variables.

   ![Выбор функции variables](./media/resource-manager-vscode-extension/add-variable.png)

   Нажмите клавишу TAB или ВВОД.

9. Для функции [variables](resource-group-template-functions-deployment.md#variables) требуется имя переменной. В скобках добавьте одинарную кавычку. Расширение предоставит имена переменных, заданных в шаблоне.

   ![Отображение переменных](./media/resource-manager-vscode-extension/show-variables.png) 

10. Выберите переменную **storageName**. Добавьте закрывающую квадратную скобку. В следующем примере показан раздел выходных данных.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
       }
   }
   ```

Окончательная версия шаблона выглядит так:

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
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
    }
  }
}
```

## <a name="deploy-template"></a>Развертывание шаблона

Теперь вы можете развернуть этот шаблон. Чтобы создать группу ресурсов, вы можете использовать PowerShell или Azure CLI. Затем можно развернуть учетную запись хранения в этой группе ресурсов.

* Для PowerShell используйте следующие команды из папки, содержащей шаблон:

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Для локальной установки Azure CLI используйте следующие команды из папки, содержащей шаблон:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

По завершении развертывания возвращаются выходные значения.

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

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения о свойствах учетной записи хранения см. в статье [Microsoft.Storage/storageAccounts template reference](/azure/templates/microsoft.storage/storageaccounts) (Справочник по шаблону Microsoft.Storage/storageAccounts).
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

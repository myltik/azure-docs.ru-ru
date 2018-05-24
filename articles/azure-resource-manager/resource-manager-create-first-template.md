---
title: Создание первого шаблона Azure Resource Manager | Документация Майкрософт
description: Пошаговые инструкции по созданию первого шаблона Azure Resource Manager. Здесь объясняется, как создать шаблон, указав в нем ссылку на учетную запись хранения.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 32ff8621eb446f9bcc45398173f47b761b929ac5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Создание и развертывание первого шаблона Azure Resource Manager
В этой статье рассматриваются действия по созданию первого шаблона Azure Resource Manager. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md). Если вы уже развернули ресурсы и хотите получить для них шаблон, см. статью [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).

Для создания и изменения шаблонов нужен редактор JSON, например [Visual Studio Code](https://code.visualstudio.com/). Это упрощенный кроссплатформенный редактор с открытым исходным кодом. Для создания шаблонов Resource Manager мы настоятельно рекомендуем использовать Visual Studio Code. В этом документе предполагается, что вы используете VS Code. Но вы можете использовать и другой редактор JSON (например, Visual Studio).

## <a name="prerequisites"></a>предварительным требованиям

* Visual Studio Code. При необходимости это средство можно установить со страницы [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Локальная установка [Azure PowerShell](/powershell/azure/install-azurerm-ps) или [Azure CLI](/cli/azure/install-azure-cli). Для работы с этим руководством нужна локальная установка, так как шаблон сохраняется в виде локального файла. Чтобы использовать облачную среду, необходимо [загрузить шаблон в учетную запись хранения](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell).

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

Ваш шаблон теперь выглядит так:

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

## <a name="use-autocomplete"></a>Использование автозаполнения

До недавнего времени работа с шаблоном заключалась только в копировании и вставке кода JSON из этой статьи. Но при разработке собственных шаблонов необходимо найти и указать свойства и значения, доступные для типа ресурса. VS Code считывает схему для типа ресурса и предлагает свойства и значения. Чтобы увидеть, как работает функция автозаполнения, перейдите к элементу properties шаблона и добавьте новую строку. Введите кавычки. Вы увидите, что VS Code сразу же предложит имена, доступные в элементе properties.

![Отображение доступных свойств](./media/resource-manager-create-first-template/show-properties.png)

Выберите **encryption**. Введите двоеточие (:), и VS Code предложит добавить новый объект.

![Добавление объекта](./media/resource-manager-create-first-template/add-object.png)

Нажмите клавишу TAB или ВВОД, чтобы добавить объект.

Снова введите кавычки. Теперь VS Code предложит свойства, доступные для encryption.

![Отображение свойств для encryption](./media/resource-manager-create-first-template/show-encryption-properties.png)

Выберите **services** и продолжайте добавлять значения, предлагаемые расширениями VS Code, пока не получится следующее:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Вы включили шифрование больших двоичных объектов для учетной записи хранения. Но программа VS Code обнаружила проблему. Обратите внимание, что для encryption получено предупреждение.

![Предупреждение для encryption](./media/resource-manager-create-first-template/encryption-warning.png)

Чтобы просмотреть предупреждение, наведите указатель мыши на зеленую линию.

![Отсутствует свойство](./media/resource-manager-create-first-template/missing-property.png)

Вы увидите, что для элемента encryption требуется свойство keySource. Введите запятую после объекта services и добавьте свойство keySource. VS Code предлагает **Microsoft.Storage** в качестве допустимого значения. По завершении элемент properties будет выглядеть следующим образом:

```json
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
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Развертывание зашифрованного хранилища

Снова разверните шаблон и укажите имя новой учетной записи хранения.

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Для интерфейса командной строки Azure:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
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

## <a name="next-steps"></a>Дополнительная информация
* Чтобы упростить разработку шаблонов, вы можете установить расширение VS Code. Дополнительные сведения см. в статье [Создание шаблона Azure Resource Manager c помощью расширения Visual Studio Code](resource-manager-vscode-extension.md).
* Дополнительные сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Дополнительные сведения о свойствах учетной записи хранения см. в статье [Microsoft.Storage/storageAccounts template reference](/azure/templates/microsoft.storage/storageaccounts) (Справочник по шаблону Microsoft.Storage/storageAccounts).
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

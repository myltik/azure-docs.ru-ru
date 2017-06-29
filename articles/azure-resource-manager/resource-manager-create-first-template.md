---
title: "Создание первого шаблона Azure Resource Manager | Документация Майкрософт"
description: "Пошаговые инструкции по созданию первого шаблона Azure Resource Manager. Здесь объясняется, как создать шаблон, указав в нем ссылку на учетную запись хранения."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/18/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 80fd9d79652e4f0d9c4c524e3a762bcc3462bb53
ms.contentlocale: ru-ru
ms.lasthandoff: 06/01/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Создание первого шаблона Azure Resource Manager
В этой статье рассматриваются действия по созданию первого шаблона Azure Resource Manager. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md). Если вы уже развернули ресурсы и хотите получить для них шаблон, см. статью [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).

Для создания и изменения шаблонов нужен редактор JSON, например [Visual Studio Code](https://code.visualstudio.com/). Это упрощенный кроссплатформенный редактор с открытым исходным кодом. Он поддерживает создание и редактирование шаблонов Resource Manager с помощью расширения. В этой статье предполагается, что вы используете VS Code. Но вы можете использовать и другой редактор JSON (например, Visual Studio).

## <a name="get-vs-code-and-extension"></a>Получение редактора VS Code и расширения
1. При необходимости установите VS Code со страницы [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Установите расширение [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Для этого воспользуйтесь Quick Open (CTRL+P) и выполните следующую команду: 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. Перезапустите VSCode, когда будет предложено включить расширение.

## <a name="create-blank-template"></a>Создание пустого шаблона

Сначала мы создадим пустой шаблон, который включает только основные разделы.

1. Создайте файл. 

2. Скопируйте и вставьте в него следующий синтаксис JSON:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. Сохраните файл как **azuredeploy.json**. 

## <a name="add-storage-account"></a>Добавление учетной записи хранения
1. Чтобы определить для развертывания учетную запись хранения, укажите ее в разделе шаблона **resources**. Значения, доступные для учетной записи хранения, см. в [справочнике по шаблонам учетных записей хранения](/azure/templates/microsoft.storage/storageaccounts). Скопируйте код JSON, который отображается для учетной записи хранения. 

3. Вставьте код JSON в раздел шаблона **resources**, как показано в примере ниже. 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-12-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  VS Code может указывать на то, что 2016-12-01 не является допустимой версией API. Если вы используете номер версии из справочной документации по шаблонам, это предупреждение можно игнорировать. Это предупреждение отображается, если схема не обновлена до последнего номера версии от поставщика ресурсов. 
  
  Предыдущий пример включает множество значений-заполнителей и некоторые свойства, которые, возможно, не потребуются в вашей учетной записи хранения.

## <a name="set-values-for-storage-account"></a>Настройка значений для учетной записи хранения

Теперь вы можете указать значения для своей учетной записи хранения. 

1. Еще раз откройте [справочник по шаблонам учетных записей хранения](/azure/templates/microsoft.storage/storageaccounts), откуда вы скопировали код JSON. В справочнике есть несколько таблиц, в которых описаны свойства и приведены доступные значения. 

2. Обратите внимание, что элементы **properties**, **customDomain**, **encryption** и **accessTier** отмечены как необязательные. В некоторых ситуациях эти значения могут потребоваться, но для упрощения нашего примера мы их удалим.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-12-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. Сейчас для элемента **kind** указано значение-заполнитель (string). В редакторе VS Code есть много функций, которые помогают понять используемые в шаблоне значения. Обратите внимание, VS Code указывает, что это значение — недопустимое. Если навести указатель мыши на значение string, VS Code предложит для элемента **kind** значения `Storage` или `BlobStorage`. 

   ![VS Code показывает предлагаемые значения](./media/resource-manager-create-first-template/vs-code-show-values.png)

   Чтобы просмотреть доступные значения, удалите символы между двойными кавычками и нажмите клавиши **CTRL+ПРОБЕЛ**. Из предложенных вариантов выберите значение **Storage**.
  
   ![Показать Intellisense](./media/resource-manager-create-first-template/intellisense.png)

   Если вы не используете редактор VS Code, откройте страницу справочника по шаблонам учетных записей хранения. Обратите внимание, что описание содержит те же допустимые значения. Установите для элемента значение **Storage**.

   ```json
   "kind": "Storage",
   ```

Ваш шаблон теперь выглядит так:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Добавление функции шаблона

Использование функций в шаблоне упрощает его синтаксис и позволяет получить значения, доступные только при его развертывании. Полный список функций шаблонов см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).

Чтобы указать, что учетная запись хранения развертывается в том же расположении, что и группа ресурсов, установите для свойства **location** следующее значение:

```json
"location": "[resourceGroup().location]",
```

VS Code опять предложит доступные функции. 

![Отображение функций](./media/resource-manager-create-first-template/show-functions.png)

Обратите внимание, что функция заключена в квадратные скобки. Функция [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) возвращает объект со свойством `location`. Группа ресурсов содержит все связанные ресурсы для вашего решения. Свойство location можно жестко задать прямо в коде (например, указать значение "Central US"), но если вы потом захотите развернуть этот шаблон в другом расположении, вам нужно будет изменить шаблон вручную. Используя функцию `resourceGroup`, вы сможете легко развернуть этот шаблон еще раз в другую группу ресурсов в другом расположении.

Ваш шаблон теперь выглядит так:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>Добавление параметров и переменных
В шаблоне осталось настроить только два значения: **name** и **sku.name**. Для этих свойств нужно добавить параметры, которые позволяют настраивать эти значения во время развертывания. 

Для имен учетных записей хранения существует несколько ограничений, которые усложняют настройку имен. Имя должно содержать от 3 до 24 символов, состоять только из цифр и букв нижнего регистра и быть уникальным. Вместо того чтобы подбирать уникальное значение, которое соответствует ограничениям, используйте функцию [uniqueString](resource-group-template-functions-string.md#uniquestring), которая создает хеш-значение. Чтобы это хеш-значение было осмысленным, добавьте префикс, который поможет после развертывания идентифицировать значение как учетную запись хранения. 

1. Чтобы передать префикс имени, который соответствует соглашению об именовании, перейдите в раздел шаблона **parameters**. Добавьте параметр в шаблон, который принимает префикс для имени учетной записи хранения:

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  Длина префикса не может превышать 11 символов, поскольку функция `uniqueString` возвращает 13 символов, а имя учетной записи не должно превышать 24 символов. Если значение параметра не передается во время развертывания, используется значение по умолчанию.

2. Перейдите к разделу шаблона **variables**. Чтобы создать имя из префикса и уникальной строки, добавьте следующую переменную:

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. В разделе **resources** укажите эту переменную в качестве имени учетной записи хранения.

   ```json
   "name": "[variables('storageName')]",
   ```

3. Чтобы настроить передачу в код разных значений SKU для учетной записи хранения, перейдите к разделу **parameters**. После параметра префикса имени учетной записи хранения добавьте параметр, который определяет допустимые значения SKU и значение по умолчанию. Допустимые значения можно найти на странице справочника по шаблонам. Они также указываются в редакторе VS Code. В следующем примере мы укажем все допустимые значения SKU. Но вы можете ограничить допустимые значения только теми типами SKU, которые хотите развернуть с помощью этого шаблона.

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
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
     }
   },
   ```

3. Измените свойство SKU так, чтобы в нем использовалось значение из параметра:

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. Сохраните файл.

## <a name="final-template"></a>Окончательная версия шаблона

После завершения действий, описанных в этой статье, шаблон будет выглядеть так:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
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
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Шаблон готов, и теперь вы можете развернуть его в своей подписке. Инструкции по развертыванию см. в статье [Развертывание ресурсов в Azure](resource-manager-quickstart-deploy.md).
* Дополнительные сведения о структуре шаблона см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).


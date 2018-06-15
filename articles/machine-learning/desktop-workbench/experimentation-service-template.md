---
title: Создание Экспериментирования в Машинном обучении с помощью шаблона Azure Resource Manager | Документы Microsoft
description: В этой статье приведен пример создания учетной записи службы "Экспериментирование в Машинном обучении Azure" с помощью шаблона Azure Resource Manager.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 65efee0eea5e2595ef8833788281c5ba60ca3a43
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830788"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Настройка службы "Экспериментирование в Машинном обучении Azure"

## <a name="overview"></a>Обзор
Учетная запись, рабочая область и проект службы "Экспериментирование в Машинном обучении Azure" являются ресурсами Azure. Таким образом, их можно развернуть с помощью шаблонов Resource Manager. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Развертывание шаблона
Чтобы развернуть шаблон, нужно выполнить всего лишь несколько действий в интерфейсе командной строки Azure или на портале Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Развертывание шаблона из командной строки
С помощью интерфейса командной строки всего лишь одна команда может развернуть шаблон в имеющуюся группу ресурсов.
Ознакомьтесь со следующими сведениями о создании шаблонов.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Развертывание шаблона с портала Azure
При желании для создания и развертывания шаблона можно также использовать портал Azure. Сделайте следующее:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **Все службы** и выполните поиск по слову "шаблоны".
3. Выберите **Шаблоны**.
4. Щелкните **+ Добавить** и скопируйте данные шаблона. 
5. Выберите шаблон, созданный на шаге 4, и нажмите кнопку **Развернуть**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Создание шаблона на основе имеющегося ресурса Azure на портале Azure
Если у вас уже есть учетная запись службы "Экспериментирование в Машинном обучении Azure", можно создать шаблон на основе этого ресурса на [портале Azure](https://portal.azure.com). 

1. Перейдите к учетной записи службы "Экспериментирование Azure" на [портале Azure](https://portal.azure.com).
2. В разделе **Параметры** щелкните **Сценарий автоматизации**.
3. Скачайте шаблон. 

Кроме того, вы можете вручную отредактировать файлы шаблонов. Ознакомьтесь со следующим примером файлов шаблона и параметров. 

### <a name="template-file-example"></a>Пример файла шаблона
Создайте файл с именем template-file.json, содержащий приведенные ниже данные. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Параметры 
Создайте файл, содержащий приведенные ниже данные, и сохраните его как <parameters.json>. 

Вы можете изменить три значения. 
* Имя учетной записи. Имя учетной записи экспериментирования.
* Расположение. Один из поддерживаемых регионов Azure.
* SKU учетной записи хранения. Машинное обучение Azure поддерживает только стандартное хранилище и не поддерживает хранилище класса Premium. Дополнительные сведения о хранилище см. в статье [Введение в хранилище Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Дополнительная информация
* [Create Azure Machine Learning preview accounts and install Azure Machine Learning Workbench](../service/quickstart-installation.md) (Создание учетных записей для предварительной версии Машинного обучения Azure и установка Azure Machine Learning Workbench).

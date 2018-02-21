---
title: "Разработка шаблонов для Azure Stack | Документация Майкрософт"
description: "Ознакомьтесь с рекомендациями по использованию шаблона Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: helaw
ms.openlocfilehash: b9109c58b29d5f09f1a86068a87c5e7f839228af
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Рекомендации по использованию шаблона Azure Resource Manager

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

При разработке приложения очень важно обеспечить мобильность шаблона в контексте взаимодействия Azure и Azure Stack.  Эта статья содержит рекомендации по разработке [шаблонов](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) Azure Resource Manager. С ее помощью вы сможете создать прототип приложения и протестировать развертывание в Azure без доступа к среде Azure Stack.

## <a name="resource-provider-availability"></a>Доступность поставщика ресурсов
Шаблон, который вы планируете развернуть, должен использовать службу Microsoft Azure, которая уже доступна или находится в предварительной версии в Azure Stack.

## <a name="public-namespaces"></a>Общедоступные пространства имен
Так как среда Azure Stack размещена в центре обработки данных, она использует собственные пространства имен конечных точек службы, а не пространства общедоступного облака Azure. Поэтому при попытке развернуть в Azure Stack встроенные общедоступные конечные точки в шаблонах Resource Manager происходит ошибка. Вместо этого можно использовать функцию *создания ссылки* и *объединения*, чтобы динамически создать конечную точку службы на основе значений, полученных от поставщика ресурсов во время развертывания. Например, чтобы не указывать *blob.core.windows.net* в шаблоне, получите [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) для динамической настройки конечной точки *osDisk.URI*.

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Управление версиями API
В Azure и Azure Stack могут быть разные версии служб Azure. Для каждого ресурса требуется атрибут apiVersion, который определяет доступные возможности. Чтобы вы могли обеспечить совместимость версий API в Azure Stack, ниже приведены допустимые версии API для каждого поставщика ресурсов.

| Поставщик ресурсов | версия_API |
| --- | --- |
| Среда выполнения приложений |`'2015-06-15'` |
| Сеть |`'2015-06-15'`, `'2015-05-01-preview'` |
| Хранилище |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| Хранилище ключей | `'2015-06-01'` |
| Служба приложений |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Функции шаблонов
[Функции](../../azure-resource-manager/resource-group-template-functions.md) Resource Manager позволяют создавать динамические шаблоны. Например, можно использовать функции для выполнения следующих задач.

* Объединение или обрезание строк. 
* Создание ссылок на значения других ресурсов.
* Итерация по ресурсам для развертывания нескольких экземпляров. 

При создании шаблонов некоторые функции не доступны в Пакете средств разработки Azure Stack и не должны использоваться. Это следующие функции:

* Skip
* Take

## <a name="resource-location"></a>Расположение ресурса
Шаблоны Resource Manager используют атрибут расположения для размещения ресурсов во время развертывания. В Azure расположения называются регионами, например "западная часть США"или "Южная Америка". В Azure Stack используются другие расположения, так как Azure Stack находится в вашем центре обработки данных.  Чтобы шаблоны можно было передавать между Azure и Azure Stack, необходимо указать расположение группы ресурсов при развертывании отдельных ресурсов. Для этого используйте функцию `[resourceGroup().Location]`, чтобы ресурсы наследовали расположение группы ресурсов.  В следующем фрагменте кода шаблона Resource Manager приведен пример использования этой функции при развертывании учетной записи хранения.

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Дополнительная информация
* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
* [Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)
* [Развертывание шаблонов с помощью Visual Studio](azure-stack-deploy-template-visual-studio.md)


---
title: "Шаблон Resource Manager для хранилища | Документация Майкрософт"
description: "Демонстрирует схему диспетчера ресурсов для развертывания учетных записей хранения с помощью шаблона."
services: azure-resource-manager,storage
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 25d35683-fe99-4a11-8b1a-b80accab58e7
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: b089bc075ec3ec718eb21357bbbc5c4af09f4af1
ms.openlocfilehash: b01125160875e367b9b6e1d02031c5dd80b41594
ms.lasthandoff: 01/05/2017


---
# <a name="storage-account-template-schema"></a>Учетная запись хранения — схема шаблона
Создание учетной записи хранения.

## <a name="schema-format"></a>Формат схемы
Чтобы создать учетную запись хранения, добавьте следующую схему в раздел ресурсов шаблона.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Значения
В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| --- | --- |
| type |Перечисление.<br />Обязательно<br />**Microsoft.Storage/storageAccounts**<br /><br />Тип создаваемого ресурса. |
| версия_API |Перечисление.<br />Обязательно<br />**2015-06-15** или **2015-05-01-preview**.<br /><br />Версия API, которая будет использована для создания ресурса. |
| Имя |Строка<br />Обязательно<br />От 3 до 24 знаков — только цифры и строчные буквы.<br /><br />Имя учетной записи хранения для создания. Имя должно быть уникальным в Azure. Мы рекомендуем использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring) в рамках соглашения об именовании, как показано в примере ниже. |
| location |Строка<br />Обязательно<br />Регион, который поддерживает учетные записи хранения. Определить допустимые регионы можно в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions).<br /><br />Регион для размещения учетной записи хранения. |
| properties |Объект<br />Обязательно<br />[объект properties](#properties)<br /><br />Объект, который определяет тип создаваемой учетной записи хранения. |

<a id="properties" />

### <a name="properties-object"></a>объект properties
| Имя | Значение |
| --- | --- |
| accountType |Строка<br />Обязательно<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS** или **Premium_LRS**.<br /><br />Тип хранилища для учетной записи хранения. Допустимые значения обозначают соответственно: локально избыточное (цен. категория «Стандартный»), избыточное в пределах зоны (цен. категория «Стандартный»), геоизбыточное (цен. категория «Стандартный»), геоизбыточное с доступом для чтения (цен. категория «Стандартный»), локально избыточное (цен. категория Премиум»). Информацию об этих типах хранилищ см. в статье [Репликация службы хранилища Azure](../storage/storage-redundancy.md). |

## <a name="examples"></a>Примеры
Следующий пример развертывает учетную запись хранения с типом хранилища «Локально избыточное (цен. категория «Стандартный»)» и уникальным именем, основанным на идентификаторе группы ресурсов.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска
Существует много шаблонов быстрого запуска, включающих учетную запись хранения. Следующие шаблоны иллюстрируют некоторые распространенные сценарии:

* [Создание стандартной учетной записи хранения](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
* [Простое развертывание виртуальной машины Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Простое развертывание виртуальной машины Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Создание профиля CDN, конечной точки сети CDN с помощью учетной записи хранения в качестве источника] https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account)
* [Создание фермы высокой доступности SharePoint с 9 виртуальными машинами с помощью расширения Powershell DSC](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
* [Простое развертывание защищенного кластера Service Fabric с 5 узлами с включенной системой диагностики Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)
* [Создание виртуальной машины на основе образа Windows с 4 пустыми дисками данных](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о службе хранилища см. в статье [Введение в хранилище Microsoft Azure](../storage/storage-introduction.md).
* Примеры шаблонов для использования новой учетной записи хранения на виртуальной машине см. в статьях [Deploy a simple Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) (Развертывание простой виртуальной машины Linux) и [Deploy a simple Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/) (Развертывание простой виртуальной машины Windows).



<properties
   pageTitle="Шаблон диспетчера ресурсов для службы хранилища | Microsoft Azure"
   description="Демонстрирует схему диспетчера ресурсов для развертывания учетных записей хранения с помощью шаблона."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Учетная запись хранения — схема шаблона

Создание учетной записи хранения.

## Формат схемы

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

## Значения

В следующих таблицах описаны значения, которые следует указать в этой схеме.

| Имя | Значение |
| ---- | ---- |
| type | Перечисление.<br />Обязательное значение.<br />**Microsoft.Storage/storageAccounts**<br /><br />Тип создаваемого ресурса. |
| версия\_API | Перечисление.<br />Обязательное значение.<br />**2015-06-15** или **2015-05-01-preview**<br /><br />Версия API, которая будет использована для создания ресурса. | 
| name | Строка.<br />Обязательное значение.<br />От 3 до 24 знаков, допускаются только цифры и строчные буквы.<br /><br />Имя учетной записи хранения для создания. Имя должно быть уникальным в Azure. Мы рекомендуем использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring) в рамках соглашения об именовании, как показано в примере ниже. |
| location | Строка.<br />Обязательное значение.<br />Область, которая поддерживает учетные записи хранения. Сведения об определении допустимых регионов см. в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions).<br /><br />Регион для размещения учетной записи хранения. |
| properties | Объект.<br />Обязательное значение.<br />[Объект свойств.](#properties)<br /><br />Объект, который определяет тип создаваемой учетной записи хранения. |

<a id="properties" />
### объект properties

| Имя | Значение |
| ---- | ---- | 
| accountType | Строка.<br />Обязательное значение.<br />**Standard\_LRS**, **Standard\_ZRS**, **Standard\_GRS**, **Standard\_RAGRS** или **Premium\_LRS**<br /><br />Тип учетной записи хранения. Допустимые значения обозначают соответственно: локально избыточное (цен. категория «Стандартный»), избыточное в пределах зоны (цен. категория «Стандартный»), геоизбыточное (цен. категория «Стандартный»), геоизбыточное с доступом для чтения (цен. категория «Стандартный»), локально избыточное (цен. категория Премиум»). Информацию об этих типах хранилищ см. в статье [Репликация службы хранилища Azure](./storage/storage-redundancy.md). |

	
## Примеры

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

## Шаблоны быстрого запуска

Существует много шаблонов быстрого запуска, включающих учетную запись хранения. Следующие шаблоны иллюстрируют некоторые распространенные сценарии:

- [Создание стандартной учетной записи хранения](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Простое развертывание виртуальной машины Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Простое развертывание виртуальной машины Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Создание профиля CDN, конечной точки сети CDN с помощью учетной записи хранения в качестве источника](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Создание фермы высокой доступности SharePoint с 9 виртуальными машинами с помощью расширения Powershell DSC](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Простое развертывание защищенного кластера Service Fabric с 5 узлами с включенной системой диагностики Azure](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Создание виртуальной машины на основе образа Windows с 4 пустыми дисками данных](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## Дальнейшие действия

- Дополнительные сведения о службе хранилища см. в статье [Знакомство со службой хранилища Microsoft Azure](./storage/storage-introduction.md).
- Примеры шаблонов для использования новой учетной записи хранения на виртуальной машине см. в статьях [Развертывание простой виртуальной машины Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) и [Развертывание простой виртуальной машины Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=AcomDC_0406_2016-->
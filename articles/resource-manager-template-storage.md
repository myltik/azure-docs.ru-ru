<properties
   pageTitle="Шаблон диспетчера ресурсов для службы хранилища | Microsoft Azure"
   description="Демонстрирует схему диспетчера ресурсов для развертывания учетных записей хранения с помощью шаблона."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
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

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Да | **Microsoft.Storage/storageAccounts** | Тип создаваемого ресурса. |
| версия\_API | enum | Да | **2015-06-15** <br /> **2015-05-01-preview** | Версия API, которая будет использоваться для создания ресурса. | 
| name | строка | Да | От 3 до 24 символов — только цифры и строчные буквы. | Имя создаваемой учетной записи хранения. Имя должно быть уникальным в Azure. Мы рекомендуем использовать функцию [uniqueString](resource-group-template-functions.md#uniquestring) в рамках соглашения об именовании, как показано в примере ниже. |
| location | строка | Да | Определить допустимые регионы можно в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions). | Регион для размещения учетной записи хранения. |
| properties | object | Да | (показано ниже) | Объект, который определяет тип создаваемой учетной записи хранения.

### properties объект

| Имя | Тип | Обязательно | Допустимые значения | Описание |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | строка | Да | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | Тип хранилища для учетной записи хранения. Допустимые значения обозначают соответственно: локально избыточное (цен. категория «Стандартный»), избыточное в пределах зоны (цен. категория «Стандартный»), геоизбыточное (цен. категория «Стандартный»), геоизбыточное с доступом для чтения (цен. категория «Стандартный»), локально избыточное (цен. категория Премиум»). Информацию об этих типах хранилищ см. в статье [Репликация службы хранилища Azure](./storage/storage-redundancy.md). |

	
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

- [Создание стандартной учетной записи хранения](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
- [Простое развертывание виртуальной машины Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
- [Простое развертывание виртуальной машины Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
- [Создание профиля CDN, конечной точки сети CDN с помощью учетной записи хранения в качестве источника](https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account)
- [Создание фермы высокой доступности SharePoint с 9 виртуальными машинами с помощью расширения Powershell DSC](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
- [Простое развертывание защищенного кластера Service Fabric с 5 узлами с включенной системой диагностики Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Создание виртуальной машины на основе образа Windows с 4 пустыми дисками данных](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)


## Дальнейшие действия

- Дополнительные сведения о службе хранилища см. в статье [Знакомство со службой хранилища Microsoft Azure](./storage/storage-introduction.md).
- Примеры шаблонов для использования новой учетной записи хранения на виртуальной машине см. в статьях [Развертывание простой виртуальной машины Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) и [Развертывание простой виртуальной машины Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=AcomDC_0107_2016-->
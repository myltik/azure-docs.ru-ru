---
title: Развертывание шаблона Azure с токена SAS и PowerShell | Документы Майкрософт
description: Используйте Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure из шаблона, защищенного с помощью токена SAS.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: f138cceb88cb9a43efdd3f11b24203378a288286
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602985"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell

Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS). В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для предоставления токена SAS во время развертывания. 

## <a name="add-private-template-to-storage-account"></a>Добавление частного шаблона в учетную запись хранения

Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [!IMPORTANT]
> Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.
> 
> 

Приведенный ниже пример позволяет настроить контейнер учетной записи хранения и передать шаблон:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Предоставление маркера SAS во время развертывания
Чтобы развернуть частный шаблон в учетной записи хранения, создайте маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона. Задайте срок действия, достаточный для выполнения развертывания.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Дополнительная информация
* Вводные сведения о развертывании шаблонов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).
* Полный пример сценария, развертывающего шаблон, см. в статье [Сценарий для развертывания шаблона Resource Manager](resource-manager-samples-powershell-deploy.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).

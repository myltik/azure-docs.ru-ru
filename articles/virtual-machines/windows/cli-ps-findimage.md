---
title: "Поиск и выбор образов виртуальных машин Windows | Документация Майкрософт"
description: "Узнайте, как определить издателя, предложение и номер SKU для образов при создании виртуальной машины Windows с помощью развертывания посредством диспетчера ресурсов."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28bb214570fcca94c5ceb6071c4851b81ec00c8d
ms.lasthandoff: 04/27/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Просмотр и выбор образов виртуальных машин Windows в Azure с помощью PowerShell
В этой статье описывается, как найти сведения об издателях образов виртуальных машин, предложениях, номерах SKU и версиях для каждого расположения, в котором может выполняться развертывание. Для примера вот некоторые часто используемые образы виртуальных машин Windows:

## <a name="table-of-commonly-used-windows-images"></a>Таблица часто используемых образов Windows
| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014 WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014 WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>Поиск образов Azure с помощью PowerShell
> [!NOTE]
> Установите и настройте [последнюю версию Azure PowerShell](/powershell/azure/overview). Если вы используете модули Azure PowerShell версии ниже 1.0, то можете использовать приведенные ниже команды. Но сначала необходимо выполнить команду `Switch-AzureMode AzureResourceManager`. 
> 
> 

При создании виртуальной машины с помощью диспетчера ресурсов Azure иногда бывает нужно указать образ по сочетанию следующих свойств:

* ИЗДАТЕЛЬ
* ПРЕДЛОЖЕНИЕ
* SKU

Например, эти значения нужны при вызове командлета PowerShell `Set-AzureRMVMSourceImage` либо при использовании файла шаблона группы ресурсов, в котором необходимо указать тип создаваемой виртуальной машины.

Чтобы определить эти значения для нужного образа, необходимо выполнить указанные ниже действия.

1. Получить список издателей образов.
2. Получить список предложений нужного издателя.
3. Получить список номеров SKU для требуемого предложения.

Во-первых, выведите список издателей с помощью следующих команд.

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Укажите название выбранного издателя и выполните следующие команды.

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Укажите название выбранного предложения и выполните следующие команды.

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Команда `Get-AzureRMVMImageSku` возвращает всю информацию, необходимую для указания образа для новой виртуальной машины.

Ниже представлен полный пример.

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Для издателя MicrosoftWindowsServer:

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Для предложения WindowsServer:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Скопируйте выбранное наименование SKU из этого списка. Теперь у вас есть все необходимые сведения, чтобы использовать командлет PowerShell `Set-AzureRMVMSourceImage` или шаблон группы ресурсов.

## <a name="next-steps"></a>Дальнейшие действия
Теперь мы можем выбрать именно тот образ, который нам нужен. Инструкции по быстрому созданию виртуальной машины на основе полученных данных образа или с помощью шаблона с этими данными см. в статье [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


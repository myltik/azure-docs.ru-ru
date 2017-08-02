---
title: "Выбор образов виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Узнайте, как использовать Azure PowerShell для определения издателя, предложения, номера SKU и версии для образов виртуальных машин из Marketplace."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Поиск образов виртуальных машин Windows в Azure Marketplace с помощью Azure PowerShell

В этой статье описывается, как с помощью Azure PowerShell находить образы виртуальных машин в Azure Marketplace. Воспользуйтесь этими сведениями, чтобы указать образ из Marketplace при создании виртуальной машины Windows.

Убедитесь, что у вас установлена и настроена последняя версия [модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).



## <a name="table-of-commonly-used-windows-images"></a>Таблица часто используемых образов Windows
| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Поиск определенных образов


При создании виртуальной машины с помощью диспетчера ресурсов Azure иногда бывает нужно указать образ по сочетанию следующих свойств:

* ИЗДАТЕЛЬ
* ПРЕДЛОЖЕНИЕ
* SKU

Например, используйте эти значения с командлетом PowerShell [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) либо с шаблоном группы ресурсов, в котором необходимо указать тип создаваемой виртуальной машины.

Чтобы определить эти значения для определенных образов, можно выполнить командлеты [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) и [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). Определите эти значения:

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

Выходные данные команды `Get-AzureRMVMImageSku` содержат всю информацию, необходимую для указания образа для новой виртуальной машины.

Ниже представлен полный пример.

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Выходные данные:

```
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
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Выходные данные:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

Для предложения WindowsServer:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Выходные данные:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

Скопируйте выбранное наименование SKU из этого списка. Теперь у вас есть все необходимые сведения, чтобы использовать командлет PowerShell `Set-AzureRMVMSourceImage` или шаблон группы ресурсов.

## <a name="next-steps"></a>Дальнейшие действия
Теперь мы можем выбрать именно тот образ, который нам нужен. Инструкции по быстрому созданию виртуальной машины на основе полученных данных образа см. в статье [Создание виртуальной машины Windows с помощью PowerShell](quick-create-powershell.md).


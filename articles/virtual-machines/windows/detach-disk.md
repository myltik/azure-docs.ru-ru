---
title: Отключение диска данных от виртуальной машины Windows в Azure | Документация Майкрософт
description: Узнайте, как отключить диск данных от виртуальной машины в Azure с использованием модели развертывания с помощью Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: e56e9ce22cc9e2bad75c944c20bff812d8720d18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913507"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Отключение диска от виртуальной машины Windows
Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища.

> [!WARNING]
> Если отключить диск, он не удаляется автоматически. Если вы подписаны на хранилище уровня "Премиум", с вас по-прежнему будет взиматься плата за хранение этого диска. Дополнительные сведения см. в разделе [Цены и выставление счетов](premium-storage.md#pricing-and-billing).
>
>

Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

## <a name="detach-a-data-disk-using-the-portal"></a>Отключение диска данных с помощью портала

1. В меню слева выберите **Виртуальные машины**.
2. Выберите виртуальную машину, от которой нужно отключить диск данных, и щелкните **Остановить**, чтобы отменить ее распределение.
3. В области виртуальной машины выберите **Диски**.
4. В верхней части области **Диски** щелкните **Изменить**.
5. В области **Диски** справа от диска данных, который требуется отключить, нажмите кнопку отключения ![Изображение кнопки отключения](./media/detach-disk/detach.png).
5. Удалив диск, в верхней части области нажмите кнопку **Сохранить**.
6. В области виртуальной машины щелкните **Обзор** и нажмите кнопку **Запустить** (вверху области), чтобы перезапустить виртуальную машину.



Диск остается в хранилище, но более не подключен к виртуальной машине.

## <a name="detach-a-data-disk-using-powershell"></a>Отключение диска данных с помощью PowerShell
В этом примере первая команда возвращает виртуальную машину **MyVM07** в группе ресурсов **RG11**, используя командлет [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm), и сохраняет полученное значение в переменной **$VirtualMachine**.

Вторая строка удаляет из виртуальной машины диск данных с именем DataDisk3, используя командлет [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

Третья строка обновляет состояние виртуальной машины с помощью командлета [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm), после чего процесс удаления диска данных завершается.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Дополнительные сведения см. в разделе [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Дополнительная информация
Если вы хотите повторно использовать диск данных, то можете просто [подключить его к другой виртуальной машине](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


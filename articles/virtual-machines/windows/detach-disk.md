---
title: "Отключение диска данных от виртуальной машины Windows в Azure | Документация Майкрософт"
description: "Узнайте, как отключить диск данных от виртуальной машины в Azure с использованием модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: bbbd31313db44d32a829e9e4c6c9b5fd9c0e533e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Отключение диска от виртуальной машины Windows
Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища.

> [!WARNING]
> Если отключить диск, он не удаляется автоматически. Если вы подписаны на хранилище уровня "Премиум", с вас по-прежнему будет взиматься плата за хранение этого диска. Дополнительные сведения см. в разделе [Цены и выставление счетов](premium-storage.md#pricing-and-billing).
>
>

Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

## <a name="detach-a-data-disk-using-the-portal"></a>Отключение диска данных с помощью портала
1. В главном меню портала выберите **Виртуальные машины**.
2. Выберите виртуальную машину, от которой нужно отключить диск данных, и щелкните **Остановить**, чтобы отменить ее распределение.
3. В колонке "Виртуальная машина" выберите **Диски**.
4. В верхней части колонки **Диски** выберите **Изменить**.
5. В колонке **Диски** справа от диска данных, который требуется отключить, нажмите кнопку ![Отключить](./media/detach-disk/detach.png).
5. После удаления диска в верхней части колонки нажмите кнопку "Сохранить".
6. В колонке виртуальной машины щелкните **Обзор** и нажмите кнопку **Запустить** вверху, чтобы перезапустить виртуальную машину.



Диск остается в хранилище, но более не подключен к виртуальной машине.

## <a name="detach-a-data-disk-using-powershell"></a>Отключение диска данных с помощью PowerShell
В этом примере первая команда возвращает виртуальную машину **MyVM07** в группе ресурсов **RG11** с помощью командлета Get-AzureRmVM. Она сохраняет имя виртуальной машины в переменной **$VirtualMachine** .

Вторая команда удаляет диск данных DataDisk3 из этой виртуальной машины.

Последняя команда обновляет состояние виртуальной машины, чтобы завершить процесс удаления диска данных.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

Дополнительные сведения см. в разделе [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите повторно использовать диск данных, то можете просто [подключить его к другой виртуальной машине](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


---
title: "Отключение диска данных от виртуальной машины Windows | Документация Майкрософт"
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
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 730672a8b52ef37c759b5c92bc1e36d8477da3f3


---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Отключение диска от виртуальной машины Windows
Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища. 

> [!WARNING]
> Если отключить диск, он не удаляется автоматически. Если вы подписаны на хранилище уровня "Премиум", с вас по-прежнему будет взиматься плата за хранение этого диска. Дополнительные сведения см. в разделе [Цены и выставление счетов](../storage/storage-premium-storage.md#pricing-and-billing). 
> 
> 

Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.  

## <a name="detach-a-data-disk-using-the-portal"></a>Отключение диска данных с помощью портала
1. В главном меню портала выберите **Виртуальные машины**.
2. Выберите виртуальную машину, к которой подключен диск данных, предназначенный для отключения, и щелкните **Все параметры**.
3. В колонке **Параметры** выберите **Диски**.
4. В колонке **Диски** выберите диск данных, который необходимо отключить.
5. В колонке этого диска данных щелкните **Отключить**.

    ![Снимок экрана, показывающий кнопку "Отключить".](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Диск остается в хранилище, но более не подключен к виртуальной машине.

## <a name="detach-a-data-disk-using-powershell"></a>Отключение диска данных с помощью PowerShell
В этом примере первая команда возвращает виртуальную машину **MyVM07** в группе ресурсов **RG11** с помощью командлета Get-AzureRmVM. Она сохраняет имя виртуальной машины в переменной **$VirtualMachine** . 

Вторая команда удаляет диск данных DataDisk3 из этой виртуальной машины. 

Последняя команда обновляет состояние виртуальной машины, чтобы завершить процесс удаления диска данных.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```


Дополнительные сведения см. в разделе [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx).

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите повторно использовать диск данных, то можете просто [подключить его к другой виртуальной машине](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->



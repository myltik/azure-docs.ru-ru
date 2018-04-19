---
title: Общие команды PowerShell для виртуальных машин Azure | Документация Майкрософт
description: Общие команды PowerShell, позволяющие приступить к созданию виртуальных машин Windows в Azure и управлению ими.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: f84473e73a32da43cc6cc80b21deb49ab4f3ceb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Общие команды PowerShell для создания виртуальных машин Azure и управления ими

В этой статье рассматриваются некоторые из команд Azure PowerShell, которые можно использовать для создания виртуальных машин в подписке Azure и управления ими.  Для получения более подробной справки о конкретных параметрах командной строки можно использовать *команду* **Get-Help**.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Как установить и настроить службы Azure PowerShell](/powershell/azure/overview).

При выполнении нескольких команд, описываемых в этой статье, могут пригодиться приведенные ниже переменные.

- $location — расположение виртуальной машины. Можно использовать командлет [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation), чтобы найти [географический регион](https://azure.microsoft.com/regions/), который вы используете.
- $myResourceGroup — имя группы ресурсов, содержащей виртуальную машину.
- $myVM — имя виртуальной машины.

## <a name="create-a-vm"></a>Создание виртуальной машины

| Задача | Get-Help |
| ---- | ------- |
| Создание конфигурации виртуальной машины |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Конфигурация виртуальной машины используется для определения или обновления параметров виртуальной машины. Для инициализации конфигурации используется имя виртуальной машины и ее [размер](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Добавление параметров конфигурации |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Параметры операционной системы, в том числе [учетные данные](https://technet.microsoft.com/library/hh849815.aspx), добавляются в объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig. |
| Добавление сетевого интерфейса |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>У виртуальной машины должен быть [сетевой интерфейс](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) для обмена данными в виртуальной сети. Можно также использовать командлет [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) для получения существующего объекта сетевого интерфейса. |
| Указание образа платформы |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "имя_издателя" -Offer "предложение_издателя" -Skus "SKU_продукта" -Version "последняя_версия"<BR></BR><BR></BR>В объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig, добавляется [информация об образе](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Объект, возвращенный этой командой, используется только в том случае, если диск операционной системы настроен для использования образа платформы. |
| Настройка диска операционной системы для использования образа платформы |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>В объект конфигурации, созданный ранее, добавляется имя диска операционной системы и его расположение в [хранилище](../../storage/common/storage-powershell-guide-full.md). |
| Настройка диска операционной системы для использования универсального образа |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>В объект конфигурации добавляются имя диска операционной системы, расположение исходного образа и расположение диска в [хранилище](../../storage/common/storage-powershell-guide-full.md). |
| Настройка диска операционной системы для использования специализированного образа |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Создание виртуальной машины |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Все ресурсы создаются в [группе ресурсов](../../azure-resource-manager/powershell-azure-resource-manager.md). Прежде чем выполнить эту команду, выполните командлеты New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface и Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Получение информации о виртуальных машинах

| Задача | Get-Help |
| ---- | ------- |
| Вывод списка виртуальных машин в подписке |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Вывод списка виртуальных машин в группе ресурсов |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Для получения списка групп ресурсов в подписке используйте командлет [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Получение информации о виртуальной машине |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Управление виртуальными машинами
| Задача | Get-Help |
| --- | --- |
| Запуск виртуальной машины |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Остановка виртуальной машины |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Перезапуск выполняющейся виртуальной машины |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Удаление виртуальной машины |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Подготовка виртуальной машины к использованию |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Выполните эту команду перед выполнением командлета Save-AzureRmVMImage. |
| Запись виртуальной машины |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Чтобы виртуальную машину можно было использовать для создания образа, необходимо [подготовить ее, завершить ее работу и подготовить к использованию](prepare-for-upload-vhd-image.md). Прежде чем выполнить эту команду, выполните командлет Set-AzureRmVm. |
| Обновление виртуальной машины |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Получите текущую конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM, измените параметры конфигурации в объекте виртуальной машины, после чего выполните данную команду. |
| Добавление диска данных в виртуальную машину |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Используйте Get-AzureRmVM, чтобы получить объект виртуальной машины. Укажите номер LUN и размер диска. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины. Добавляемый вами диск не инициализирован. |
| Удаление диска данных от виртуальной машины |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Используйте Get-AzureRmVM, чтобы получить объект виртуальной машины. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины. |
| Добавление расширения в виртуальную машину |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Выполните данную команду с использованием соответствующих [сведений о конфигурации](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) для расширения, которое вы хотите установить. |
| Удаление расширения виртуальной машины |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с основными шагами по созданию виртуальной машины в разделе [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


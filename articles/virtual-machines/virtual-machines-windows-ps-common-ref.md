---
title: "Общие команды PowerShell для виртуальных машин | Документация Майкрософт"
description: "Общие команды PowerShell, позволяющие приступить к созданию виртуальных машин в Azure в среде Windows и управлению этими виртуальными машинами."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d86db0f0487c8c3627fa91b656db6a4c1576348c


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Общие команды PowerShell для создания виртуальных машин и управления ими
В этой статье рассматриваются некоторые из команд Azure PowerShell, которые можно использовать для создания виртуальных машин в подписке Azure и управления ими.  Для получения более подробной справки о конкретных параметрах командной строки можно использовать *команду* **Get-Help**.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-a-vm"></a>Создание виртуальной машины
| Задача | Get-Help |
| --- | --- |
| Создание конфигурации виртуальной машины |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "имя_виртуальной_машины" -VMSize "размер_виртуальной_машины"<BR></BR><BR></BR>Конфигурация виртуальной машины используется для определения или обновления параметров виртуальной машины. Для инициализации конфигурации используется имя виртуальной машины и ее [размер](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Добавление параметров конфигурации |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "имя_компьютера" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Параметры операционной системы, в том числе [учетные данные](https://technet.microsoft.com/library/hh849815.aspx), добавляются в объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig. |
| Добавление сетевого интерфейса |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>У виртуальной машины должен быть [сетевой интерфейс](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) для обмена данными в виртуальной сети. Можно также использовать командлет [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) для получения существующего объекта сетевого интерфейса. |
| Указание образа платформы |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "имя_издателя" -Offer "предложение_издателя" -Skus "SKU_продукта" -Version "последняя_версия"<BR></BR><BR></BR>В объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig, добавляется [информация об образе](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Объект, возвращенный этой командой, используется только в том случае, если диск операционной системы настроен для использования образа платформы. |
| Настройка диска операционной системы для использования образа платформы |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "имя_диска" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>В объект конфигурации, созданный ранее, добавляется имя диска операционной системы и его расположение в [хранилище](../storage/storage-powershell-guide-full.md). |
| Настройка диска операционной системы для использования универсального образа |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "имя_диска" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/имя_диска.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>В объект конфигурации добавляются имя диска операционной системы, расположение исходного образа и расположение диска в [хранилище](../storage/storage-powershell-guide-full.md). |
| Настройка диска операционной системы для использования специализированного образа |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "имя_диска" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Создание виртуальной машины |[New-AzureRmVM]() -ResourceGroupName "имя_группы_ресурсов" -Location "имя_расположения" -VM $vm<BR></BR><BR></BR>Все ресурсы создаются в [группе ресурсов](../powershell-azure-resource-manager.md). Виртуальная машина должна быть создана в же [расположении](https://msdn.microsoft.com/library/azure/dn495177.aspx) , в котором находится группа ресурсов. Прежде чем выполнить эту команду, выполните командлеты New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface и Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Получение информации о виртуальных машинах
| Задача | Команда |
| --- | --- |
| Вывод списка виртуальных машин в подписке |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| Вывод списка виртуальных машин в группе ресурсов |Get-AzureRmVM -ResourceGroupName "имя_группы_ресурсов"<BR></BR><BR></BR>Для получения списка групп ресурсов в подписке используйте командлет [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx). |
| Получение информации о виртуальной машине |Get-AzureRmVM -ResourceGroupName "имя_группы_ресурсов" -Name "имя_ВМ" |

## <a name="manage-vms"></a>Управление виртуальными машинами
| Задача | Команда |
| --- | --- |
| Запуск виртуальной машины |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_виртуальной_машины" |
| Остановка виртуальной машины |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_виртуальной_машины" |
| Перезапуск выполняющейся виртуальной машины |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_виртуальной_машины" |
| Удаление виртуальной машины |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_виртуальной_машины" |
| Подготовка виртуальной машины к использованию |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "имя_виртуальной_машины" -Generalized<BR></BR><BR></BR>Выполните эту команду перед выполнением командлета Save-AzureRmVMImage. |
| Запись виртуальной машины |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "имя_группы_ресурсов" -VMName "имя_виртуальной_машины" -DestinationContainerName "контейнер_образа" -VHDNamePrefix "префикс_имени_образа" -Path "C:\путь_к_файлу\имя_файла.json"<BR></BR><BR></BR>Чтобы виртуальную машину можно было использовать для создания образа, необходимо [завершить ее работу и подготовить ее к использованию](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Прежде чем выполнить эту команду, выполните командлет Set-AzureRmVm. |
| Обновление виртуальной машины |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "имя_группы_ресурсов" -VM $vm<BR></BR><BR></BR>Получите текущую конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM, измените параметры конфигурации в объекте виртуальной машины, после чего выполните данную команду. |
| Добавление диска данных в виртуальную машину |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "имя_диска" -VhdUri "https://mystore1.blob.core.windows.net/vhds/имя_диска.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Используйте Get-AzureRmVM, чтобы получить объект виртуальной машины. Укажите номер LUN и размер диска. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины. Добавляемый вами диск не инициализирован. Сведения об инициализации дисков при добавлении см. в статье [Управление виртуальными машинами Azure с помощью диспетчера ресурсов и PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Удаление диска данных от виртуальной машины |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "имя_диска"<BR></BR><BR></BR>Используйте Get-AzureRmVM, чтобы получить объект виртуальной машины. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины. |
| Добавление расширения в виртуальную машину |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "имя_группы_ресурсов" -Location "расположение_Azure" -VMName "имя_виртуальной_машины" -Name "имя_расширения" -Publisher "имя_издателя" -Type "тип_расширения" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Выполните данную команду с использованием соответствующих [сведений о конфигурации](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) для расширения, которое вы хотите установить. |
| Удаление расширения виртуальной машины |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_расширения" -VMName "имя_виртуальной_машины" |

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с основными шагами по созданию виртуальной машины в разделе [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Dec16_HO2-->



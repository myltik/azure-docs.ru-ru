<properties 
   pageTitle="Общие команды PowerShell для виртуальных машин | Microsoft Azure"
   description="Общие команды PowerShell, позволяющие приступить к созданию виртуальных машин в Azure в среде Windows и управлению этими виртуальными машинами."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# Общие команды PowerShell для создания виртуальных машин и управления ими

В этой статье рассматриваются некоторые из команд Azure PowerShell, которые можно использовать для создания виртуальных машин в подписке Azure и управления ими. Для получения более подробной справки о конкретных параметрах командной строки можно использовать *команду* **Get-Help**.

## Создание ресурсов с помощью Azure PowerShell

Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и войти в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Задача | Команда
-------------- | -------------------------
Создание конфигурации виртуальной машины | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "имя\_ВМ" -VMSize "размер\_ВМ"<BR></BR><BR></BR>Конфигурация виртуальной машины используется для определения или обновления параметров виртуальной машины. Для инициализации конфигурации используется имя виртуальной машины и ее [размер](virtual-machines-windows-sizes.md).
Добавление параметров конфигурации | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "имя\_компьютера" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Параметры операционной системы, в том числе [учетные данные](https://technet.microsoft.com/library/hh849815.aspx), добавляются в объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig.
Добавление сетевого интерфейса | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>У виртуальной машины должен быть [сетевой интерфейс](virtual-machines-windows-ps-create.md) для обмена данными в виртуальной сети. Можно также использовать командлет [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) для получения существующего объекта сетевого интерфейса.
Указание образа платформы | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "имя\_издателя" -Offer "предложение\_издателя" -Skus "SKU\_продукта" -Version "последняя\_версия"<BR></BR><BR></BR>В объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig, добавляется [информация об образе](virtual-machines-windows-cli-ps-findimage.md). Объект, возвращенный этой командой, используется только в том случае, если диск операционной системы настроен для использования образа платформы.
Настройка диска операционной системы для использования образа платформы | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "имя\_диска" -VhdUri "http://mystore1.blob.core.windows.net/vhds/имя\_диска.vhd" -CreateOption FromImage<BR></BR><BR></BR>В объект конфигурации, созданный ранее, добавляются имя диска операционной системы и его целевое расположение в [хранилище](../storage/storage-powershell-guide-full.md).
Настройка диска операционной системы для использования универсального образа | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "имя\_диска" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/имя\_диска.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>В объект конфигурации, созданный ранее, добавляются имя диска операционной системы, расположение исходного образа и расположение диска в [хранилище](../storage/storage-powershell-guide-full.md).
Настройка диска операционной системы для использования специализированного образа | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "имя\_диска" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
Создание виртуальной машины | [New-AzureRmVM]() -ResourceGroupName "имя\_группы\_ресурсов" -Location "имя\_расположения" -VM $vm<BR></BR><BR></BR>Все ресурсы создаются в [группы ресурсов](../powershell-azure-resource-manager.md). Виртуальная машина должна быть создана в же [расположении](https://msdn.microsoft.com/library/azure/dn495177.aspx), в котором находится группа ресурсов. Прежде чем выполнить эту команду, выполните командлеты New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface и Set-AzureRmVMOSDisk.
Вывод списка виртуальных машин в подписке| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Вывод списка виртуальных машин в группе ресурсов | Get-AzureRmVM -ResourceGroupName "имя\_группы\_ресурсов"<BR></BR><BR></BR>Для получения списка групп ресурсов в подписке используйте командлет [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Получение информации о виртуальной машине | Get-AzureRmVM -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ"
Запуск виртуальной машины | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ"
Остановка виртуальной машины | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ"
Перезапуск выполняющейся виртуальной машины | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ"
Удаление виртуальной машины | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ"
Подготовка виртуальной машины к использованию | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_ВМ" -Generalized<BR></BR><BR></BR>Эту команду нужно выполнить перед выполнением командлета Save-AzureRmVMImage.
Запись виртуальной машины | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -VMName "имя\_ВМ" -DestinationContainerName "контейнер\_образа" -VHDNamePrefix "префикс\_имени\_образа" -Path "C:\\путь\_к\_файлу\\имя\_файла.json"<BR></BR><BR></BR>Чтобы виртуальную машину можно было использовать для создания образа, необходимо [завершить ее работу и подготовить ее к использованию](virtual-machines-windows-capture-image.md). Прежде чем выполнить эту команду, выполните командлет Set-AzureRmVm.
Обновление виртуальной машины | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -VM $vm<BR></BR><BR></BR>Получите текущую конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM, измените параметры конфигурации в объекте виртуальной машины, после чего выполните данную команду.
Добавление диска данных в виртуальную машину | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "имя\_диска" -VhdUri "https://mystore1.blob.core.windows.net/vhds/имя\_диска.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Используйте командлет Get-AzureRmVM для получения объекта виртуальной машины. Укажите номер LUN и размер диска. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины. Добавляемый диск не инициализирован. Чтобы узнать, как это сделать, ознакомьтесь с разделом [Управление виртуальными машинами Azure с помощью диспетчера ресурсов и PowerShell](virtual-machines-windows-ps-manage.md).
Удаление диска данных от виртуальной машины | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "имя\_диска"<BR></BR><BR></BR>Используйте Get-AzureRmVM, чтобы получить объект виртуальной машины. Выполните командлет Update-AzureRmVM, чтобы применить изменения к конфигурации виртуальной машины.
Добавление расширения в виртуальную машину | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Location "расположение\_Azure" -VMName "имя\_ВМ" -Name "имя\_расширения" -Publisher "имя\_издателя" -Type "тип\_расширения" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Выполните данную команду с использованием соответствующих [сведений о конфигурации](virtual-machines-windows-extensions-configuration-samples.md) для расширения, которое вы хотите установить.
Удаление расширения виртуальной машины | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "имя\_группы\_ресурсов" -Name "имя\_расширения" -VMName "имя\_ВМ"

## Дальнейшие действия

- Ознакомьтесь с основными шагами по созданию виртуальной машины в разделе [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](virtual-machines-windows-ps-create.md).

<!---HONumber=AcomDC_0629_2016-->
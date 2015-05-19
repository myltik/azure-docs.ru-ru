<properties
   pageTitle="Общие задачи виртуальных машин с использованием режима диспетчера ресурсов в Azure PowerShell"
   description="Описывает команды для выполнения общих задач виртуальных машин с использованием режима диспетчера ресурсов в Azure PowerShell."
   services="virtual-machines"
   documentationCenter="windows"
   authors="KBDAzure"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="kathydav;singhkay"/>

# Общие задачи виртуальных машин с использованием Azure PowerShell в режиме диспетчера ресурсов

 > [AZURE.SELECTOR]
    - [Resource Manager](virtual-machines-manage-powershell-resource-management.md)
    - [Service Management](virtual-machines-manage-powershell-service-management.md)

Многие повседневные задачи управления виртуальными машинами можно автоматизировать с помощью командлетов Azure PowerShell в режиме диспетчера ресурсов. В этой статье приводятся примеры команд для простых задач, а также ссылки на статьи о командах для более сложных задач.

>[AZURE.NOTE]Если вы еще не установили и не настроили Azure PowerShell, то [здесь](install-configure-powershell.md) можно получить инструкции.

## Как использовать примеры команд
Вам понадобится заменить определенный текст в командах в соответствии с вашей средой. Символы < and > обозначают текст, который необходимо заменить. При замене текста удалите символы, но оставьте на месте кавычки.

## Получение виртуальной машины
Вы будете выполнять эту основную задачу очень часто. Используйте ее, чтобы получить информацию о виртуальной машине, выполнить задачи в виртуальной машине или получить выходные данные, которые необходимо сохранить в переменной.

Чтобы получить информацию о виртуальной машине, выполните эту команду и замените все, что заключено в кавычки, включая символы < and >:

     Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Если выполнить команду с именем группы ресурсов «MyVM» именем виртуальной машины «MyRG», вы увидите примерно следующее:

<!-- need image-->

Чтобы сохранить выходные данные в переменной $vm, выполните:

    $vm = Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

<!--need this log-on syntax 
## Log on to a Windows-based virtual machine

Run these commands:

>[AZURE.NOTE] You can get the virtual machine and cloud service name from the display of the **Get-AzureVM** command.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "\" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch 
-->

## Запуск виртуальной машины

Выполните следующую команду:

    Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

## Остановка виртуальной машины

Выполните следующую команду:

    Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Используйте параметр **StayProvisioned**, чтобы сохранить виртуальный IP-адрес \(VIP\) облачной службы, если эта виртуальная машина является последней в этой службе. При использовании этого параметра вам по-прежнему будут выставлять счета за использование виртуальной машины.

## Перезапуск виртуальной машины

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

## Подключение диска данных
Для этой задачи требуется несколько шагов. Во-первых, используйте командлет \*\*\*\*Add-AzureDataDisk\*\*\*\*, чтобы добавить диск в объект $vm, затем выполните командлет Update-AzureVM, чтобы обновить конфигурацию виртуальной машины.

Вам необходимо решить, какой диск подключать — новый диск или диск с данными. При подключении нового диска команда создает VHD-файл и присоединяет его.

Чтобы подключить новый диск, выполните следующую команду:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Чтобы подключить существующие диски данных, выполните следующую команду:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Чтобы подключить диски данных из существующего VHD-файла в хранилище больших двоичных объектов, выполните следующую команду:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Создание виртуальной машины Windows

Чтобы создать виртуальную машину под управлением Windows в Azure, см. указания в разделе [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md). В этом разделе последовательно описывается создание набора команд PowerShell, создающего виртуальную машину Windows, для которой можно предварительно настроить:

- членство в домене Active Directory;
- дополнительные диски;
- членство в существующем наборе балансировки нагрузки;
- статический IP-адрес.


<!--HONumber=52-->

---
title: Управление виртуальными машинами с помощью Azure PowerShell | Microsoft Docs
description: Узнайте о командах, которые можно использовать для автоматизации задач управления виртуальными машинами.
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2016
ms.author: kasing

---
# Управление виртуальными машинами с помощью Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Многие повседневные задачи управления виртуальными машинами можно автоматизировать с помощью командлетов Azure PowerShell. В этой статье приводятся примеры команд для простых задач, а также ссылки на статьи о командах для более сложных задач.

> [!NOTE]
> Если вы еще не установили и не настроили Azure PowerShell, соответствующие указания см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
> 
> 

## Как использовать примеры команд
Вам понадобится заменить определенный текст в командах в соответствии с вашей средой. Знаки < и > обозначают текст, который необходимо заменить. При замене текста удалите символы, но оставьте на месте кавычки.

## Получение виртуальной машины
Вы будете выполнять эту основную задачу очень часто. Используйте ее, чтобы получить информацию о виртуальной машине, выполнить задачи в виртуальной машине или получить выходные данные, которые необходимо сохранить в переменной.

Чтобы получить информацию о виртуальной машине, выполните эту команду и замените все, что заключено в кавычки, в том числе знаки < и >.

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Чтобы сохранить выходные данные в переменной $vm, выполните:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Вход в виртуальную машину под управлением Windows
Выполните следующие команды:

> [!NOTE]
> Имя виртуальной машины и облачной службы можно получить из вывода команды **Get-AzureVM**.
> 
> $svcName = "<cloud service name>"
> $vmName = "<virtual machine name>"
> $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
> $localFile = $localPath + "" + $vmname + ".rdp"
> Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## Остановка виртуальной машины
Выполните следующую команду:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Используйте этот параметр, чтобы сохранить виртуальный IP-адрес (VIP) облачной службы, если эта виртуальная машина является последней в этой службе. <br><br> При использовании параметра StayProvisioned вам по-прежнему будут выставлять счета за использование виртуальной машины.
> 
> 

## Запуск виртуальной машины
Выполните следующую команду:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Присоединение диска данных
Для этой задачи требуется несколько шагов. Сначала используйте командлет ****Add-AzureDataDisk****, чтобы добавить диск в объект $vm. Затем используйте командлет **Update-AzureVM**, чтобы обновить конфигурацию виртуальной машины.

Вам необходимо решить, какой диск подключать — новый диск или диск с данными. При подключении нового диска команда создает и присоединяет VHD-файл.

Чтобы подключить новый диск, выполните следующую команду:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Чтобы подключить существующий диск, выполните следующую команду:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Чтобы подключить диски данных из существующего VHD-файла в хранилище больших двоичных объектов, выполните следующую команду:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## Создание виртуальной машины под управлением Windows
Чтобы создать виртуальную машину под управлением Windows в Azure, используйте указания в разделе [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-windows-classic-create-powershell.md). В этом разделе последовательно описано создание набора команд Azure PowerShell, создающего виртуальную машину под управлением Windows, для которой можно предварительно настроить:

* членство в домене Active Directory;
* дополнительные диски;
* членство в существующем наборе балансировки нагрузки;
* статический IP-адрес.

<!---HONumber=AcomDC_0817_2016-->
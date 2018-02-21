---
title: "Управление дисками виртуальной машины в Azure Stack | Документация Майкрософт"
description: "Подготовка дисков для виртуальных машин в Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Хранение дисков виртуальных машин для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack поддерживает использование [неуправляемых дисков](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) в виртуальной машине в качестве диска операционной системы и диска данных. Чтобы использовать неуправляемые диски, нужно создать [учетную запись хранения](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), а потом сохранить в ней диски в качестве страничных BLOB-объектов в контейнерах. Эти диски называются дисками виртуальной машины.

Чтобы повысить производительность и снизить затраты на управление системой Azure Stack, рекомендуется разместить каждый диск виртуальной машины в отдельном контейнере. Контейнер должен содержать диск ОС или диск данных, но не оба одновременно. Тем не менее ограничений, которые препятствуют помещению обоих дисков в один и тот же контейнер, нет.

При добавлении одного или нескольких дисков данных на виртуальную машину запланируйте использование дополнительных контейнеров в качестве расположения для хранения этих дисков. Как и диски данных, диски ОС для дополнительных виртуальных машин также должны размещаться в отдельных контейнерах.

При создании нескольких виртуальных машин можно повторно использовать одну и ту же учетную запись хранения для каждой из них. Только создаваемые контейнеры должны быть уникальными.  

Чтобы добавить диски на виртуальную машину, используйте пользовательский портал или PowerShell.

| Метод | Параметры
|-|-|
|[Пользовательский портал](#use-the-portal-to-add-additional-disks-to-a-vm)|— Добавьте новые диски данных на виртуальную машину, которая была подготовлена ранее. Эти диски создаются с помощью Azure Stack. </br> </br>— Добавьте имеющийся VHD-файл как диск на виртуальную машину, которая была подготовлена ранее. Для этого необходимо подготовить и отправить VHD-файл в Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | — Создайте виртуальную машину с диском ОС и в то же время добавьте на нее один или несколько дисков данных. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>Добавление дополнительных дисков на виртуальную машину с помощью портала
Когда вы используете портал для создания виртуальной машины для большинства элементов marketplace, по умолчанию создается только диск ОС. Диски, созданные с помощью Azure, называются управляемыми дисками.

После подготовки виртуальной машины можно использовать портал, чтобы добавить на нее новый или имеющийся диск данных. Каждый дополнительный нужно поместить в отдельный контейнер. Диски, добавляемые на виртуальную машину, называются неуправляемыми дисками.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>Подключение нового диска данных к виртуальной машине с помощью портала

1.  На портале выберите **Виртуальные машины**.    
    ![Пример. Панель мониторинга виртуальных машин](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Выберите виртуальную машину, которая была подготовлена ранее.   
    ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Для виртуальной машины выберите **Диски** > **Подключить новый**.       
    ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  В области **Подключить новый диск** выберите **Расположение**. По умолчанию для расположения установлен тот же контейнер, где размещен диск ОС.      
    ![Пример. Указание расположения диска](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Выберите используемую **учетную запись хранения**. Затем выберите **контейнер**, в который необходимо поместить диск данных. На странице **Контейнеры** можно создать контейнер, если это необходимо. Затем можно изменить расположение нового диска на собственный контейнер. При использовании отдельного контейнера для каждого диска распространение расположения диска данных может повысить производительность. Нажмите кнопку **Выбрать**, чтобы сохранить выбор.     
    ![Пример. Выбор контейнера](media/azure-stack-manage-vm-disks/select-container.png)

6.  На странице **Подключение нового диска** обновите параметры **Имя**, **Тип**, **Размер** и **Кэширование узлов** диска. Затем нажмите кнопку **ОК**, чтобы сохранить новую конфигурацию диска виртуальной машины.  
    ![Пример. Завершение подключения диска](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  После того как Azure Stack создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.   
    ![Пример. Просмотр диска](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Подключение существующего диска данных к виртуальной машине
1.  [Подготовьте VHD-файл](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd), который будет использоваться в качестве диска данных для виртуальной машины. Отправьте этот VHD-файл в учетную запись хранения для использования с виртуальной машиной, к которой нужно подключить этот VHD-файл.

  Запланируйте размещение VHD-файла в контейнере, отличном от того, в котором хранится диск ОС.   
  ![Пример. Отправка VHD-файла](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  После отправки VHD-файла можно подключить VHD к виртуальной машине. В меню слева выберите **Виртуальные машины**.  
 ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Затем выберите виртуальную машину из списка.    
  ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  На странице для виртуальной машины выберите **Диски** > **Подключить существующий**.   
  ![Пример. Подключение существующего диска](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  На странице **Подключение существующего диска** выберите **VHD-файл**. Откроется страница **Учетные записи хранения**.    
  ![Пример. Выбор VHD-файла](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  В разделе **Учетные записи хранения** выберите используемую учетную запись, а потом — контейнер, где размещен ранее переданный VHD-файл. Выберите VHD-файл, а потом нажмите кнопку **Выбрать**, чтобы сохранить выбор.    
  ![Пример. Выбор контейнера](media/azure-stack-manage-vm-disks/select-container2.png)

7.  В разделе **Подключение существующего диска** выбранный файл находится в списке **VHD-файл**. Обновите параметр **Кэширование узла** для диска, а затем нажмите кнопку **ОК**, чтобы сохранить новую конфигурацию диска для виртуальной машины.    
  ![Пример. Подключение VHD-файла](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  После того как Azure Stack создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.   
  ![Пример. Завершение подключения диска](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Добавление нескольких неуправляемых дисков к виртуальной машине с помощью PowerShell
Вы можете использовать PowerShell для подготовки виртуальной машины и добавления новых дисков данных или подключения уже имеющихся **VHD-файлов** в качестве дисков данных.

Командлет **Add-AzureRmVMDataDisk** добавляет диск данных к виртуальной машине. Диск данных можно добавить к новой виртуальной машине при ее создании или к имеющейся виртуальной машине. Укажите параметр **VhdUri**, чтобы распределить диски в разные контейнеры.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Добавление дисков данных в новую виртуальную машину
В приведенных ниже примерах используются команды PowerShell для создания виртуальной машины с тремя дисками данных, размещенными в различных контейнерах.

Первая команда создает объект виртуальной машины и сохраняет его в переменной *$VirtualMachine*. Команда присваивает имя и размер виртуальной машине.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Следующие три команды присваивают пути трех дисков данных переменным *$DataDiskVhdUri01*, *$DataDiskVhdUri02* и *$DataDiskVhdUri03*. Задайте имя другого пути в URL-адресе для распределения дисков в различные контейнеры.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Три окончательные команды добавляют диски данных на виртуальную машину, заданную в переменной *$VirtualMachine*. Каждая команда указывает имя, расположение и дополнительные свойства диска. Универсальный код ресурса (URI) каждого диска хранится в переменных *$DataDiskVhdUri01*, *$DataDiskVhdUri02* и *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Используйте следующие команды PowerShell, чтобы добавить конфигурацию диска ОС и сети на виртуальную машину, а затем запустите новую виртуальную машину.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Добавление дисков данных к имеющейся виртуальной машине
В следующих примерах используются команды PowerShell для добавления трех дисков данных в имеющуюся виртуальную машину.
Первая команда получает виртуальную машину с именем VirtualMachine с помощью командлета **Get-AzureRmVM**. Она сохраняет имя виртуальной машины в переменной *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Следующие три команды присваивают пути трех дисков данных переменным $DataDiskVhdUri01, $DataDiskVhdUri02 и $DataDiskVhdUri03.  Разные имена путей в vhduri указывают различные контейнеры для размещения диска.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Следующие три команды добавляют диски данных к виртуальной машине, заданной в переменной *$VirtualMachine*. Каждая команда указывает имя, расположение и дополнительные свойства диска. Универсальный код ресурса (URI) каждого диска хранится в переменных *$DataDiskVhdUri01*, *$DataDiskVhdUri02* и *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  Последняя команда обновляет состояние виртуальной машины, заданной в переменной *$VirtualMachine*, в -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о виртуальных машинах Azure Stack см. в [этих рекомендациях](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).

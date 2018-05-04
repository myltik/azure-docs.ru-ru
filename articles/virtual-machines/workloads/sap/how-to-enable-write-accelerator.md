---
title: Ускоритель записи Azure для развертываний SAP | Документация Майкрософт
description: Руководство по использованию систем SAP HANA, развернутых на виртуальных машинах Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89a4216a3893892eedd6c216c7e0e5d51cf64749
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Ускоритель записи Azure для развертываний SAP
Ускоритель записи Azure — это функция, которая предназначена исключительно для виртуальных машин серии M и недоступна для других серий виртуальных машин в Azure. Как очевидно из названия, целью функции является уменьшение задержки операций ввода-вывода записей в хранилище Azure класса Premium. 

Ускоритель записи Azure предоставляется для развертываний серии М в режиме общедоступной предварительной версии в следующих регионах:

- Западная часть США 2
- Восток США 2
- Западная Европа
- Юго-Восточная Азия

## <a name="planning-for-using-azure-write-accelerator"></a>Планирование использования ускорителя записи Azure
Эту функцию нужно использовать для томов, которые содержат журналы транзакций или повторяемых операций СУБД, а не для томов данных СУБД. Это связано с тем, что для использования ускорителя записи Azure виртуальные жесткие диски хранилища Azure класса Premium нужно подключить без дополнительного кэширования операций чтения, доступного для такого хранилища. Больше преимуществ с этим типом кэширования можно наблюдать в традиционных базах данных. Так как ускоритель записи влияет только на действия записи и не ускоряет операции чтения, для SAP поддерживается использование этой функции для журнала транзакций или журнала повторяемых операций поддерживаемых баз данных SAP. 

Ускоритель записи Azure работает только с [управляемыми дисками Azure](https://azure.microsoft.com/services/managed-disks/). Поэтому требуется соответствующее планирование. 

>[!NOTE]
> Так как функция ускорителя записи Azure все еще предоставляется в общедоступной предварительной версии, сценарии развертывания в рабочей среде пока не поддерживаются.

Есть ограничения на число виртуальных жестких дисков хранилища Azure класса Premium для каждой виртуальной машины, которые могут поддерживаться ускорителем записи Azure. Сейчас действуют такие ограничения:


| SKU виртуальной машины | Число дисков с ускорителем записи | Число операций ввода-вывода в секунду на виртуальную машину при использовании ускорителя записи |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 



> [!IMPORTANT]
> Если вы хотите включить или отключить ускоритель записи Azure для имеющегося тома, который состоит из нескольких дисков хранилища Azure класса Premium и чередующийся с диском Windows или диспетчерами томов, дисковыми пространствами Windows, масштабируемым файловым сервером Windows (SOFS), диспетчером логических томов Linux или MDADM, все диски, из которых состоит том, нужно включить или отключить для ускорителя записи с помощью отдельных шагов. **Перед включением или выключением ускорителя записи в такой конфигурации завершите работу виртуальной машины Azure**. 


> [!IMPORTANT]
> Чтобы включить ускоритель записи Azure в имеющемся диске Azure, который не является частью тома, состоящего из нескольких дисков, с диском Windows или диспетчерами томов, дисковыми пространствами Windows, масштабируемым файловым сервером Windows (SOFS), диспетчером логических томов Linux или MDADM, нужно завершить работу рабочей нагрузки с доступом к диску Azure. Нужно завершить работу приложений баз данных, использующих диск Azure.

> [!IMPORTANT]
> Включение ускорителя записи для диска операционной системы Azure виртуальной машины приведет к перезагрузке виртуальной машины. 

Включение ускорителя записи Azure для дисков ОС не является необходимым шагом для конфигураций виртуальной машины, связанных с SAP.

### <a name="restrictions-when-using-azure-write-accelerator"></a>Ограничения при использовании ускорителя записи Azure
При использовании ускорителя записи Azure для диска Azure или виртуального жесткого диска применяются ограничения ниже.

- Для кэширования диска класса "Премиум" необходимо установить значение "Нет" или "Только для чтения". Все другие режимы кэширования не поддерживаются.
- Моментальный снимок диска c включенным ускорителем записи пока не поддерживается. Это ограничение блокирует возможность службы Azure Backup создавать моментальный снимок, согласованный с приложением, всех дисков виртуальной машины.
- Ускоренный путь используется только для записи на диск операций ввода-вывода меньшего размера. При использовании рабочих нагрузок, где данные поступают в ходе массовой загрузки или где буферы журнала транзакций разных DBMS заполняются перед сохранением в хранилище в большей степени, ускоренный путь, скорее всего, не будет использоваться.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Включение ускорителя записи на определенном диске
В нескольких разделах ниже описано, как включить ускоритель записи Azure на виртуальных жестких дисках хранилища Azure класса Premium.

На данный момент Azure REST API и Power Shell — единственные методы включения ускорителя записи. Другие методы, поддерживаемые на портале Azure, будут представлены через несколько недель.

### <a name="prerequisites"></a>предварительным требованиям
Сегодня к использованию ускорителя записи Azure применяются предварительные требования ниже.

- Ваш идентификатор подписки, который использовался для развертывания виртуальной машины и хранилища для нее, должен быть добавлен в список разрешенных. Свяжитесь со своим агентом Microsoft CSA, GBB или менеджером по технической поддержке, чтобы включить идентификатор подписки в список разрешенных идентификаторов. 
- Диски, к которым вы хотите применить ускоритель записи Azure, должны быть [управляемыми дисками Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Включение ускорителя записи с помощью PowerShell
Модуль Azure PowerShell не ниже версии 5.5.0 включает изменения соответствующих командлетов для включения или отключения ускорителя записи Azure для определенных дисков хранилища Azure класса Premium.
Чтобы включить или развернуть диски, поддерживаемые ускорителем записи, команды PowerShell ниже были изменены и расширены и теперь принимают параметр для ускорителя.

В командлеты ниже был добавлен новый параметр переключения "WriteAccelerator". 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Если не задать параметр, свойству присваивается значение false и развертываются диски, которые не поддерживаются ускорителем записи Azure.

В командлеты ниже был добавлен новый параметр переключения "OsDiskWriteAccelerator". 

- Set-AzureRmVmssStorageProfile

Если не задать параметр, свойству присваивается значение false и поставляются диски, не использующие ускоритель записи Azure.

В командлеты ниже был добавлен новый необязательный логический (не допускающий значения NULL) параметр — "OsDiskWriteAccelerator". 

- Update-AzureRmVM
- Update-AzureRmVmss

Укажите значения $true или $false, чтобы управлять поддержкой ускорителя записи Azure с дисками.

Примеры команд могут выглядеть так:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Для двух основных сценариев можно написать скрипты, как показано в следующих разделах.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Добавление нового диска, поддерживаемого ускорителем записи Azure
Этот скрипт можно использовать, чтобы добавить новый диск на виртуальную машину. Диск, созданный с помощью этого скрипта, будет использовать ускоритель записи Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Вам необходимо адаптировать имена виртуальной машины, диска, группы ресурсов, размера диска и LunID на диске для определенного развертывания.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Включение ускорителя записи Azure на имеющемся диске Azure
Если нужно включить ускоритель записи на имеющемся диске, вы можете использовать этот скрипт для выполнения задачи:

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Вам необходимо адаптировать имена виртуальной машины, диска и группы ресурсов. Скрипт выше добавляет ускоритель записи на имеющийся диск. Для параметра $newstatus установлено значение "$true". При использовании значения "$false" ускоритель записи на имеющемся диске будет отключен.

> [!Note]
> Выполнение скрипта выше приведет к отключению указанного диска, включению на нем ускорителя записи и последующему подключению диска.




### <a name="enabling-through-rest-apis"></a>Включение ускорителя записи через интерфейсы REST API
Чтобы выполнить развертывание с помощью Azure REST API, необходимо установить клиент ARMClient Azure.

#### <a name="install-armclient"></a>Установка клиента ARMClient

Чтобы запустить клиент ARMClient, нужно установить его с помощью Chocolatey. Это можно сделать, используя cmd.exe или PowerShell. Чтобы выполнить эти команды, используйте запуск от имени администратора.

При использовании cmd.exe выполните следующую команду:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

При использовании PowerShell запустите командлет ниже:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Теперь вы можете установить клиент ARMClient с помощью команды ниже в cmd.exe или PowerShell.

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Получение текущей конфигурации виртуальной машины
Чтобы изменить атрибуты конфигурации диска, вам сначала нужно получить текущую конфигурацию в JSON-файле. Выполните команду ниже, чтобы получить текущую конфигурацию:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Замените термины в скобках <<   >> своими данными, включая имя файла, которое должно быть присвоено JSON-файлу.

Выходные данные будут выглядеть примерно так, как показано ниже:

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Далее нужно обновить JSON-файл и включить ускоритель записи на диске с именем "log1". Это можно сделать, добавив этот атрибут в JSON-файл после записи кэша диска. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Затем обновите имеющееся развертывание с помощью команды ниже:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Выходные данные должны выглядеть, как показано ниже: Как видно, для одного диска включен ускоритель записи.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

После реализации изменения ускоритель записи должен поддерживать диск.

 
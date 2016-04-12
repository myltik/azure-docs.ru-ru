<properties
   pageTitle="Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки | Microsoft Azure"
   description="Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки

Из этой статьи вы узнаете, как быстро создать виртуальную машину Linux в Azure с помощью команды `azure vm quick-create` (команда Azure CLI). Эта команда создает виртуальную машину с базовой инфраструктурой, которую можно использовать для создания прототипов и тестирования концепций. Для работы с этой статьей вам потребуется учетная запись Azure ([бесплатная пробная версия](https://azure.microsoft.com/pricing/free-trial/)) и [Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

## Краткая сводка по командам

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## Подробные пошаговые инструкции

### Создание виртуальной машины Linux

В следующей команде можно использовать любой образ. Для быстрого создания виртуальной машины мы будем использовать `canonical:ubuntuserver:14.04.2-LTS:latest`. (Чтобы найти образ в магазине Marketplace, [воспользуйтесь поиском](virtual-machines-linux-cli-ps-findimage.md). Также можно [отправлять собственные образы](virtual-machines-linux-create-upload-generic.md).) Должны отобразиться примерно следующие данные.

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Теперь можно подключиться по SSH к виртуальной машине через порт SSH 22 по умолчанию.

Команда `azure vm quick-create` быстро создает виртуальную машину, после чего вы можете войти на нее и начинать с ней работать. Среда этой машины простая, но если вам нужно настроить собственную среду, это можно сделать с помощью [шаблона Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md). Кроме того, вы можете [создать собственную пользовательскую среду для виртуальной машины Linux с помощью команд Azure CLI](virtual-machines-linux-cli-deploy-templates.md).

В приведенном выше примере создается:

- группа ресурсов Azure для развертывания виртуальной машины;
- учетная запись хранения Azure, в которой будет храниться VHD-файл, то есть образ виртуальной машины;
- виртуальная сеть Azure и подсеть для подключения к виртуальной машине;
- виртуальный сетевой адаптер для подключения виртуальной машины к сети;
- общедоступный IP-адрес и префикс поддомена, составляющие интернет-адрес для внешнего использования. Затем в этой среде создается виртуальная машина Linux.

Эта виртуальная машина доступна непосредственно из Интернета и защищена только именем пользователя и паролем.

## Дальнейшие действия

Вы быстро создали виртуальную машину Linux для использования в целях тестирования или демонстрации. Можно создать более безопасную среду выполнения на базе виртуальной машины Linux в Azure, выполнив следующие действия:

- [Создание виртуальной машины Linux в Azure с помощью шаблонов Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Создание в Azure виртуальной машины Linux, защищенной с помощью SSH, посредством шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Создание виртуальной машины Linux в Azure с помощью инфраструктуры CLI Azure и настройка инфраструктуры](virtual-machines-linux-create-cli-complete.md)

Для этого также имеется множество закрытых и открытых средств развертывания инфраструктуры, настройки и взаимодействия.

<!---HONumber=AcomDC_0406_2016-->
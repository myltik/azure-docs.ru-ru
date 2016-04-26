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
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки

В этой статье показано, как быстро создать в Azure виртуальную машину Linux с помощью команды `azure vm quick-create` интерфейса командной строки Azure. Команда `quick-create` создает виртуальную машину с базовой инфраструктурой, которую можно использовать для быстрой проверки или моделирования идеи. Можно считать, что это самый быстрый способ запустить командную оболочку bash Linux. Для работы с этой статьей вам потребуется учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

## Краткая сводка по командам

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Подробные пошаговые инструкции

## Создание виртуальной машины Linux

В следующей команде можно использовать любой образ. В нашем примере для быстрого создания виртуальной машины мы будем использовать `canonical:ubuntuserver:14.04.2-LTS:latest`. (Чтобы найти образ в магазине Marketplace, [воспользуйтесь поиском](virtual-machines-linux-cli-ps-findimage.md). Также можно [отправлять пользовательские образы](virtual-machines-linux-create-upload-generic.md)). Должны отобразиться примерно следующие данные.

В пошаговом руководстве ниже замените подсказки о вводе информации значениями, соответствующими вашей среде. В статье мы используем типичные примеры значений.

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Теперь вы можете зайти через SSH на виртуальную машину, используя стандартный порт SSH 22 и общедоступный IP-адрес, указанный выше в выводе команды.

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

Операция `azure vm quick-create` — это быстрый способ создать виртуальную машину, на которой можно войти в оболочку bash для выполнения команд. Однако с помощью `vm quick-create` вы не получите возможностей сложной среды. Если вам нужно настроить собственную среду, вы можете [развернуть виртуальную машину с помощью шаблонов диспетчера ресурсов Azure](virtual-machines-linux-cli-deploy-templates.md) или [создать пользовательскую среду для виртуальной машины Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md).

В приведенном выше примере создается:

- группа ресурсов Azure для развертывания виртуальной машины;
- учетная запись хранения Azure, в которой будет храниться VHD-файл, то есть образ виртуальной машины;
- виртуальная сеть Azure и подсеть для подключения к виртуальной машине;
- виртуальный сетевой адаптер для подключения виртуальной машины к сети;
- общедоступный IP-адрес и префикс поддомена, составляющие интернет-адрес для внешнего использования. Затем в этой среде создается виртуальная машина Linux.

## Дальнейшие действия

Вы быстро создали виртуальную машину Linux для использования в целях тестирования или демонстрации. Чтобы создать виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей.

- [Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md).
- [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md).
- [Создание виртуальной машины Linux с нуля с помощью Azure CLI](virtual-machines-linux-create-cli-complete.md).

Эти статьи помогут вам создать свою инфраструктуру Azure. Кроме того, вы сможете использовать разные закрытые и открытые средства для развертывания, настройки и оркестрации инфраструктуры.

<!---HONumber=AcomDC_0420_2016-->
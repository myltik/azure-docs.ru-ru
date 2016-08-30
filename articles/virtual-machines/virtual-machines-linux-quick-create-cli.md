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
   ms.date="08/18/2016"
   ms.author="v-livech"/>


# Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки

> [AZURE.NOTE] Если у вас есть несколько минут, помогите нам улучшить качество документации по виртуальным машинам Linux в Azure, поделившись своими впечатлениями в этом [небольшом опросе](https://aka.ms/linuxdocsurvey). Каждый ваш ответ помогает нам совершенствовать средства, необходимые вам для работы.

В этой статье показано, как быстро развернуть в Azure виртуальную машину Linux с помощью команды `azure vm quick-create` интерфейса командной строки Azure. Команда `quick-create` развертывает виртуальную машину с базовой инфраструктурой, которую можно использовать для быстрой проверки или создания прототипа решения. Для работы с этой статьей вам потребуется учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [интерфейс командной строки Azure](../xplat-cli-install.md) с выполненным входом (`azure login`) и в режиме Resource Manager (`azure config mode arm`). Вы также можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md).

## Краткая сводка по командам

Одна команда для развертывания виртуальной машины CoreOS и привязки ключа SSH:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Развертывание виртуальной машины Linux

Теперь мы изучим команды с описанием каждого шага на примере RedHat Enterprise Linux 7.2.

## Использование псевдонима ImageURN

Команда `quick-create` интерфейса командной строки Azure содержит псевдонимы, сопоставленные с наиболее распространенными дистрибутивами ОС. В следующей таблице перечислены псевдонимы дистрибутивов (начиная с интерфейса командной строки Azure версии 0.10). Все развертывания с использованием команды `quick-create` по умолчанию устанавливают резервные виртуальные машины в хранилище SSD, что обеспечивает высокую производительность.

| Alias | Издатель | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | последних |
| CoreOS | CoreOS | CoreOS | Stable | последних |
| Debian | credativ | Debian | 8 | последних |
| openSUSE | SUSE | openSUSE | 13\.2 | последних |
| RHEL | Redhat | RHEL | 7,2 | последних |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | последних |



Чтобы развернуть виртуальную машину RedHat Enterprise Linux 7.2, мы используем `RHEL` для параметра **ImageURN** (`-Q`). Эти псевдонимы `quick-create` представляют только некоторые ОС, поддерживаемые в Azure. Чтобы найти другие образы в Marketplace, [воспользуйтесь поиском](virtual-machines-linux-cli-ps-findimage.md). Вы также можете [передавать пользовательские образы](virtual-machines-linux-create-upload-generic.md).

В пошаговом руководстве ниже замените запросы на ввод информации значениями, актуальными для вашей среды.

Следуйте инструкциям и введите свои собственные имена.

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

В результате должен отобразиться следующий блок выходных данных.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Теперь через SSH вы можете получить доступ к виртуальной машине, используя порт 22 и общедоступный IP-адрес, указанный в выводе команды. (Также для этого можно использовать указанное полное доменное имя.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
Процесс входа в систему должен выглядеть примерно так:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Дальнейшие действия

С помощью команды `azure vm quick-create` можно быстро развернуть виртуальную машину, чтобы войти в оболочку Bash и начать работу. Команда `vm quick-create` не дает дополнительные преимущества сложной среды. Чтобы развернуть виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

- [Развертывание виртуальных машин и управление ими с помощью шаблонов Azure Resource Manager и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md).
- [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md).
- [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0824_2016-->
<properties
   pageTitle="Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки | Microsoft Azure"
   description="В этой статье описано создание виртуальной машины Linux в Azure с помощью интерфейса командной строки."
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
   ms.date="09/08/2016"
   ms.author="v-livech"/>


# Создание виртуальной машины Linux в Azure с помощью интерфейса командной строки

В этой статье мы расскажем, как быстро развернуть в Azure виртуальную машину Linux с помощью команды `azure vm quick-create` в интерфейсе командной строки Azure. Команда `quick-create` развертывает виртуальную машину в надежной базовой инфраструктуре, которую вы можете использовать для быстрой проверки или создания прототипа решения. Для работы с этой статьей вам потребуется:

- Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).

- [Интерфейс командной строки Azure](../xplat-cli-install.md) с выполненным входом (с помощью команды `azure login`).

- Интерфейс командной строки Azure _необходимо переключить_ в режим Azure Resource Manager с помощью `azure config mode arm`.

Вы также можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md).

## Быстрые команды

На примере ниже показано, как развернуть виртуальную машину CoreOS и подключить к ней ключ SSH (ваши значения аргументов могут отличаться).

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

В следующих разделах вы найдете пояснения к командам, а также требования при использовании сервера Ubuntu 14.04 LTS в качестве дистрибутива Linux.

## Использование псевдонимов команды quick-create

Чтобы быстро выбрать дистрибутив, можно воспользоваться псевдонимами интерфейса командной строки Azure для большинства распространенных дистрибутивов ОС. В следующей таблице перечислены псевдонимы (для интерфейса командной строки Azure версии 0.10). Все развертывания с использованием команды `quick-create` по умолчанию устанавливают резервные виртуальные машины с поддержкой хранилища на основе твердотельных накопителей (SSD), что обеспечивает более быструю подготовку к работе и доступ к диску с высокой производительностью. (Эти псевдонимы представляют лишь небольшую часть дистрибутивов, доступных в Azure. Чтобы найти другие образы в Azure Marketplace, [воспользуйтесь поиском](virtual-machines-linux-cli-ps-findimage.md). Также можно использовать [собственный пользовательский образ](virtual-machines-linux-create-upload-generic.md).)

| Псевдоним | Издатель | Предложение | SKU | Версия |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | последняя |
| CoreOS | CoreOS | CoreOS | стабильный | последняя |
| Debian | credativ | Debian | 8 | последняя |
| openSUSE | SUSE | openSUSE | 13\.2 | последняя |
| RHEL | Red Hat | RHEL | 7,2 | последняя |
| UbuntuLTS | Canonical | Сервер Ubuntu | 14\.04.4-LTS | последняя |

В следующих разделах для параметра **ImageURN** (`-Q`) используется псевдоним `UbuntuLTS`, чтобы развернуть виртуальную машину на базе сервера Ubuntu 14.04.4 LTS.

## Подробное пошаговое руководство

В предыдущем примере команда `quick-create` вызывала только флаг `-M` для идентификации открытого ключа SSH для его передачи при отключении паролей SSH. Теперь необходимо ввести следующие данные:

- имя группы ресурсов (для своей первой группы ресурсов в Azure вы можете выбрать любое имя);
- имя виртуальной машины;
- расположение (по умолчанию можно использовать westus или westeurope);
- Linux (для Azure необходимо указать ОС, которую вы предпочитаете использовать);
- имя пользователя.

В приведенном ниже примере указаны все необходимые значения. Так как в качестве файла открытого ключа формата SSH-RSA используется `~/.ssh/id_rsa.pub`, этот файл работает как есть.

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

В результате должен отобразиться следующий блок выходных данных.

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
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
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Войдите на виртуальную машину, используя общедоступный IP-адрес, указанный в выходных данных команды. Для этого также можно использовать указанное полное доменное имя (FQDN).

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

Процесс входа в систему должен выглядеть примерно так:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

exampleAdminUser@exampleVMName:~$
```

## Дальнейшие действия

С помощью команды `azure vm quick-create` можно быстро развернуть виртуальную машину, чтобы войти в оболочку Bash и начать работу. Однако использование `vm quick-create` не дает возможностей всестороннего контроля или создания более сложной среды. Чтобы развернуть виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

- [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md)
- [Создание виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Вы также можете [использовать драйвер Azure`docker-machine` с различными командами для быстрого создания виртуальной машины Linux в качестве узла Docker](virtual-machines-linux-docker-machine.md).

<!---HONumber=AcomDC_0914_2016-->
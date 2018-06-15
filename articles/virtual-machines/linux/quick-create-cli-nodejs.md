---
title: Создание виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Создание виртуальной машины Linux в Azure с помощью Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: ea9eab1a02a29005917848ed54ab8ba160b89b63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30901815"
---
# <a name="create-a-linux-vm-using-the-azure-cli-10"></a>Создание виртуальной машины Linux с помощью Azure CLI 1.0

В этой статье мы расскажем, как быстро развернуть в Azure виртуальную машину Linux с помощью команды `azure vm quick-create` в интерфейсе командной строки Azure. Команда `quick-create` развертывает виртуальную машину в надежной базовой инфраструктуре, которую вы можете использовать для быстрой проверки или создания прототипа решения.

> [!NOTE]
Чтобы создать виртуальную машину с использованием Azure CLI 2.0, изучите раздел [Создание виртуальной машины с помощью Azure CLI](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Вы также можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье требуется использовать [файлы открытого и закрытого ключей SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Быстрые команды

На примере ниже показано, как развернуть виртуальную машину CoreOS и подключить к ней ключ SSH (ваши значения аргументов могут отличаться).

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Следующее руководство содержит инструкции по развертыванию ВМ UbuntuLTS с подробным описанием каждого шага.

## <a name="vm-quick-create-aliases"></a>Использование псевдонимов команды quick-create

Чтобы быстро выбрать дистрибутив, можно воспользоваться псевдонимами интерфейса командной строки Azure для большинства распространенных дистрибутивов ОС. В следующей таблице перечислены псевдонимы (для интерфейса командной строки Azure версии 0.10). Все развертывания с использованием команды `quick-create` по умолчанию устанавливают резервные виртуальные машины с поддержкой хранилища на основе твердотельных накопителей (SSD), что обеспечивает более быструю подготовку к работе и доступ к диску с высокой производительностью. (Эти псевдонимы представляют лишь небольшую часть дистрибутивов, доступных в Azure. Чтобы найти другие образы в Azure Marketplace, выполните поиск в [PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [Интернете](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) или [загрузите собственный пользовательский образ](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | ИЗДАТЕЛЬ | ПРЕДЛОЖЕНИЕ | SKU | Version (версия) |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |последняя |
| CoreOS |CoreOS |CoreOS |Stable |последняя |
| Debian |credativ |Debian |8 |последняя |
| openSUSE |SUSE |openSUSE |13.2 |последняя |
| RHEL |Red Hat |RHEL |7,2 |последняя |
| UbuntuLTS |Canonical |Сервер Ubuntu |14.04.4-LTS |последняя |

В следующих разделах для параметра **ImageURN** (`-Q`) используется псевдоним `UbuntuLTS`, чтобы развернуть виртуальную машину на базе сервера Ubuntu 14.04.4 LTS.

В предыдущем примере команда `quick-create` вызывала только флаг `-M`, чтобы идентифицировать открытый ключ SSH для его передачи при отключении паролей SSH. Теперь необходимо ввести следующие аргументы:

* имя группы ресурсов (для своей первой группы ресурсов в Azure вы можете выбрать любое имя);
* имя виртуальной машины;
* расположение (по умолчанию можно использовать `westus` или `westeurope`);
* Linux (для Azure необходимо указать ОС, которую вы предпочитаете использовать);
* Имя пользователя

В приведенном ниже примере указаны все необходимые значения. Так как в качестве файла открытого ключа формата SSH-RSA используется `~/.ssh/id_rsa.pub`, этот файл работает как есть.

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

В результате должен отобразиться следующий блок выходных данных.

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
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
+ Creating VM "myVM"
+ Looking up the VM "myVM"
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
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
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

## <a name="log-in-to-the-new-vm"></a>Вход на новую ВМ
Войдите на виртуальную машину, используя общедоступный IP-адрес, указанный в выходных данных команды. Для этого также можно использовать указанное полное доменное имя.

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
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

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Дополнительная информация
С помощью команды `azure vm quick-create` можно быстро развернуть виртуальную машину, чтобы войти в оболочку Bash и начать работу. Однако использование `vm quick-create` не дает возможностей всестороннего контроля или создания более сложной среды.  Чтобы развернуть виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Вы также можете [использовать драйвер Azure `docker-machine` с разными командами для быстрого создания виртуальной машины Linux в качестве узла Docker](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

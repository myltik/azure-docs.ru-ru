---
title: "Создание виртуальной машины Linux с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Создание виртуальной машины Linux с помощью Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Создание виртуальной машины Linux с помощью Azure CLI 2.0
В этой статье рассматривается быстрое развертывание виртуальной машины Linux в Azure с помощью команды [az vm create](/cli/azure/vm#create) в Azure CLI 2.0 с использованием как управляемых дисков, так и дисков в собственных учетных записях хранения. Эти действия можно также выполнить с помощью [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Чтобы создать виртуальную машину, вам потребуется: 

* Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).
* установленный экземпляр [Azure CLI 2.0](/cli/azure/install-az-cli2);
* выполненный вход в учетную запись Azure (введите [az login](/cli/azure/#login)).

(Вы также можете развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

В примере ниже показано, как развернуть виртуальную машину Debian и подключиться к ней с помощью ключа SSH. Ваши аргументы могут отличаться. Чтобы выбрать другой образ, найдите его [здесь](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Использование управляемых дисков

Чтобы использовать управляемые диски Azure, необходимо использовать регион, поддерживающий их. Сначала введите [az group create](/cli/azure/group#create), чтобы создать группу ресурсов, которая содержит все развертываемые ресурсы:

```azurecli
 az group create -n myResourceGroup -l westus
```

Ниже показано, как выглядят выходные данные. (Вы можете выбрать другой параметр `--output`, чтобы получить другой формат.)

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Создание виртуальной машины 
Теперь вы можете создать виртуальную машину и среду для нее. Не забудьте заменить значение `--public-ip-address-dns-name` уникальным именем. Приведенное ниже имя уже может быть занято.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


Выходные данные выглядят, как показано ниже. Запишите значение `publicIpAddress` или `fqdn` для входа по протоколу **SSH** на виртуальную машину.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Войдите на виртуальную машину, используя общедоступный IP-адрес или полное доменное имя, указанные в выводе команды.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

В зависимости от выбранного дистрибутива должны появиться примерно такие выходные данные:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Сведения о других задачах, которые можно выполнять с новой виртуальной машиной с помощью управляемых дисков, см. в разделе [Дальнейшие действия](#next-steps).

## <a name="using-unmanaged-disks"></a>Использование неуправляемых дисков 

Виртуальные машины с неуправляемыми дисками используют неуправляемые учетные записи хранения. Сначала введите [az group create](/cli/azure/group#create), чтобы создать группу ресурсов для всех развертываемых ресурсов.

```azurecli
az group create --name nativedisks --location westus
```

Выходные данные выглядят следующим образом (при желании можно выбрать другой вариант `--output`):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Создание виртуальной машины 

Теперь вы можете создать виртуальную машину и среду для нее. Используйте флаг `--use-unmanaged-disk` для создания виртуальной машины с неуправляемыми дисками. Также будет создана неуправляемая учетная запись хранения. Не забудьте заменить значение `--public-ip-address-dns-name` уникальным именем. Приведенное ниже имя уже может быть занято.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

Выходные данные выглядят, как показано ниже. Запишите значение `publicIpAddress` или `fqdn` для входа по протоколу **SSH** на виртуальную машину.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Войдите на виртуальную машину, используя общедоступный IP-адрес или полное доменное имя, указанные в выводе команды выше.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

В зависимости от выбранного дистрибутива должны появиться примерно такие выходные данные:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Дальнейшие действия
С помощью команды `az vm create` можно быстро развернуть виртуальную машину, чтобы войти в оболочку Bash и начать работу. Однако использование `az vm create` не дает возможностей всестороннего контроля или создания более сложной среды.  Чтобы развернуть виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Вы также можете [использовать драйвер Azure `docker-machine` с различными командами для быстрого создания виртуальной машины Linux в качестве узла Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Если вы используете Java, попробуйте применить метод [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).



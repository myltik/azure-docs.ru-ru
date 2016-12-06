---
title: "Создание виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия) | Microsoft Azure"
description: "Создание виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)."
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2bd363e3c22f4cf4daf2e0fa352fd4a131d1675f
ms.openlocfilehash: 89db2c9f388b8a5496a306ba0a152ab57481ea50


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview"></a>Создание виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 (предварительная версия)
В этой статье мы расскажем, как быстро развернуть в Azure виртуальную машину Linux с помощью команды [az vm create](/cli/azure/vm#create) в интерфейсе командной строки Azure 2.0 (предварительная версия). 

> [!NOTE] 
> Интерфейс командной строки Azure 2.0 (предварительная версия) — это интерфейс командной строки нового поколения, поддерживающий различные платформы. Попробуйте поработать с ним и сообщите нам свое мнение на [странице проекта в GitHub](https://github.com/Azure/azure-cli).
>
> В остальных наших документах используется текущая версия интерфейса командной строки Azure. Чтобы создать виртуальную машину с использованием существующего интерфейса командной строки Azure, а не интерфейса командной строки 2.0 (предварительная версия), см. [эту статью](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Чтобы создать виртуальную машину, вам потребуется: 

* Учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)).
* [интерфейс командной строки Azure 2.0 (предварительная версия)](https://github.com/Azure/azure-cli#installation), установленный на компьютере;
* выполненный вход в учетную запись Azure (введите [az login](/cli/azure/#login)).

(Вы также можете быстро развернуть виртуальную машину Linux с помощью [портала Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

На примере ниже показано, как развернуть виртуальную машину Debian и подключить к ней ключ SSH (ваши значения аргументов могут отличаться; если вам требуется другой образ, [поищите его здесь](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Сначала введите [az resource group create](/cli/azure/resource/group#create), чтобы создать группу ресурсов, которая содержит все развертываемые ресурсы:

```azurecli
az resource group create -n myResourceGroup -l westus
```

Выходные данные выглядят следующим образом (при желании можно выбрать другой вариант `--output`):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Создание виртуальной машины с помощью последнего образа Debian

Теперь вы можете создать виртуальную машину и среду для нее. Не забудьте заменить значение `----public-ip-address-dns-name` уникальным именем. Приведенное ниже имя уже может быть занято.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


Выходные данные выглядят, как показано ниже. Запишите значение `publicIpAddress` или `fqdn` для входа по протоколу **SSH** на виртуальную машину.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Войдите на виртуальную машину, используя общедоступный IP-адрес, указанный в выходных данных команды. Для этого также можно использовать указанное полное доменное имя (FQDN).

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

В зависимости от выбранного дистрибутива должны появиться примерно такие выходные данные:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Дальнейшие действия
С помощью команды `az vm create` можно быстро развернуть виртуальную машину, чтобы войти в оболочку Bash и начать работу. Однако использование `az vm create` не дает возможностей всестороннего контроля или создания более сложной среды.  Чтобы развернуть виртуальную машину Linux, настроенную для вашей инфраструктуры, выполните инструкции, приведенные в любой из следующих статей:

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Вы также можете [использовать драйвер Azure `docker-machine` с различными командами для быстрого создания виртуальной машины Linux в качестве узла Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Если вы используете Java, попробуйте применить метод [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Nov16_HO4-->



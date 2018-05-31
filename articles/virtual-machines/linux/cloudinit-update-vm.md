---
title: Обновление и установка пакетов с помощью cloud-init на виртуальной машине Linux в Azure | Документация Майкрософт
description: Как с помощью cloud-init обновить и установить пакеты в создаваемой виртуальной машине Linux, используя Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: 8d5835b5d1b0c2f77bdf5e1a2b808478b8f4de22
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186160"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Обновление и установка пакетов с помощью cloud-init на виртуальной машине Linux в Azure
В этой статье показано, как с помощью [cloud-init](https://cloudinit.readthedocs.io) обновлять пакеты на виртуальной машине или в масштабируемом наборе виртуальных машин при подготовке в Azure. Эти скрипты cloud-init выполняются при первой загрузке, если в Azure подготовлены все нужные ресурсы. Дополнительные сведения о встроенной поддержке cloud-init в Azure и поддерживаемых дистрибутивах Linux см. в [обзоре cloud-init](using-cloud-init.md).

## <a name="update-a-vm-with-cloud-init"></a>Обновление виртуальной машины с помощью cloud-init
В целях безопасности вы можете настроить автоматическую установку последних обновлений на виртуальной машине при первом запуске. Так как cloud-init одинаково работает в разных дистрибутивах Linux, нет необходимости указывать `apt` или `yum` для диспетчера пакетов. Вместо этого следует указать `package_upgrade`, а процесс cloud-init самостоятельно определит правильный механизм для используемого дистрибутива. Этот рабочий процесс позволяет использовать один и тот же скрипт cloud-init с разными дистрибутивами.

Чтобы увидеть процесс обновления в действии, создайте файл с именем *cloud_init_hostname.txt* и вставьте в него конфигурацию, приведенную ниже. Для этого примера создайте файл в Cloud Shell (не на локальном компьютере). Вы можете использовать любой редактор. Введите `sensible-editor cloud_init_upgrade.txt`, чтобы создать файл и просмотреть список доступных редакторов. Выберите первый пункт, чтобы использовать редактор **nano**. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Прежде чем развернуть этот образ, необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create) и укажите файл cloud-init с помощью `--custom-data cloud_init_upgrade.txt`, как показано ниже.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины, который указан в выходных данных предыдущей команды. Введите свой общедоступный IP-адрес в качестве значения для **publicIpAddress**, как показано здесь:

```bash
ssh <publicIpAddress>
```

Запустите средство управления пакетами и проверьте наличие обновлений.

```bash
sudo yum update
```

После того как cloud-init проверит и установит обновления в процессе загрузки, больше доступных для установки обновлений не должно оставаться.  Вы можете просмотреть процесс обновления, количество измененных пакетов, а также установить `httpd`. Для этого выполните команду `yum history` и просмотрите выходные данные, аналогичные приведенным ниже.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные примеры изменения конфигурации с помощью cloud-init см. в следующих статьях:
 
- [Добавление пользователя Linux к виртуальной машине](cloudinit-add-user.md)
- [Запуск диспетчера пакетов для обновления существующих пакетов при первой загрузке](cloudinit-update-vm.md)
- [Изменение имени локального узла виртуальной машины](cloudinit-update-vm-hostname.md) 
- [Установка пакета приложения, обновление файлов конфигурации и внедрение ключей](tutorial-automate-vm-deployment.md)
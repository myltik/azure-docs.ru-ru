---
title: Настройка файла подкачки на виртуальной машине Linux с помощью cloud-init | Документация Майкрософт
description: Как с помощью cloud-init и Azure CLI 2.0 настроить файл подкачки на виртуальной машине Linux
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
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952604"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Настройка файла подкачки на виртуальной машине Linux с помощью cloud-init
В этой статье показано, как с помощью [cloud-init](https://cloudinit.readthedocs.io) настроить файл подкачки в разных дистрибутивах Linux. Файл подкачки традиционно настраивался агентом Linux (WALA) в зависимости от того, каким дистрибутивам он требовался.  В этом документе описано, как создать файл подкачки по запросу во время подготовки с использованием cloud-init.  Дополнительные сведения о встроенной поддержке cloud-init в Azure и поддерживаемых дистрибутивах Linux см. в [обзоре cloud-init](using-cloud-init.md).

## <a name="create-swapfile-for-ubuntu-based-images"></a>Создание файла подкачки для образов на основе Ubuntu
По умолчанию в Azure файлы подкачки не создаются образами из коллекции Ubuntu. Инструкции по включению настройки файла подкачки во время подготовки виртуальной машины с помощью cloud-init, см. в [документации по AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) на вики-сайте Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Создание файла подкачки для образов на основе RedHat и CentOS

В текущей оболочке создайте файл *cloud_init_swapfile.txt* и вставьте в него следующую конфигурацию. Для этого примера создайте файл в Cloud Shell (не на локальном компьютере). Вы можете использовать любой редактор. Введите `sensible-editor cloud_init_swapfile.txt`, чтобы создать файл и просмотреть список доступных редакторов. Выберите первый пункт, чтобы использовать редактор **nano**. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Прежде чем развернуть этот образ, необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create) и укажите файл cloud-init с помощью `--custom-data cloud_init_swapfile.txt`, как показано ниже.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Проверка создания файла подкачки
Подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины, который указан в выходных данных предыдущей команды. Введите свой общедоступный IP-адрес в качестве значения для **publicIpAddress**, как показано здесь:

```bash
ssh <publicIpAddress>
```

Подключившись по протоколу SSH к виртуальной машине, проверьте, создан ли файл подкачки.

```bash
swapon -s
```

Выходные данные этой команды должны выглядеть следующим образом:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Если в существующем образе Azure настроен файл подкачки, и необходимо изменить его конфигурацию для новых образов, следует удалить существующий файл подкачки. Дополнительные сведения см. в документе "Customize Images to provision by cloud-init" (Настройка образов для подготовки с помощью cloud-init).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные примеры изменения конфигурации с помощью cloud-init см. в следующих статьях:
 
- [Добавление пользователя Linux к виртуальной машине](cloudinit-add-user.md)
- [Запуск диспетчера пакетов для обновления существующих пакетов при первой загрузке](cloudinit-update-vm.md)
- [Изменение имени локального узла виртуальной машины](cloudinit-update-vm-hostname.md) 
- [Установка пакета приложения, обновление файлов конфигурации и внедрение ключей](tutorial-automate-vm-deployment.md)

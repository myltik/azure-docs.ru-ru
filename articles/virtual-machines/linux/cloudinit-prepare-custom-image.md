---
title: Подготовка образа виртуальной машины Azure для использования с cloud-init | Документация Майкрософт
description: Подготовка существующего образа виртуальной машины Azure к развертыванию с помощью cloud-init
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
ms.openlocfilehash: dda444e77f588cd1ba5989b393e9a3987241ef9a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Подготовка существующего образа виртуальной машины Azure Linux к использованию с cloud-init
В этой статье показано, как выбрать и подготовить существующую виртуальную машину Azure к повторному развертыванию и применению cloud-init. Из полученного образа можно развернуть новую виртуальную машину или масштабируемый набор виртуальных машин. И то, и другое вы сможете дополнительно настроить во время развертывания с помощью cloud-init.  Эти скрипты cloud-init выполняются при первой загрузке, если в Azure подготовлены все нужные ресурсы. Дополнительные сведения о встроенной поддержке cloud-init в Azure и поддерживаемых дистрибутивах Linux см. в [обзоре cloud-init](using-cloud-init.md).

## <a name="prerequisites"></a>предварительным требованиям
В этом документе предполагается, что у вас уже есть виртуальная машина Azure под управлением поддерживаемой версии ОС Linux. На этой виртуальной машине должны быть выполнены все необходимые настройки, установлены все необходимые модули, применены все необходимые обновления и проведены все проверки на соответствие требованиям. 

## <a name="preparing-rhel-74--centos-74"></a>Подготовка RHEL 7.4 и CentOS 7.4
Чтобы установить cloud-init, войдите на виртуальную машину Linux с помощью SSH и выполните следующие команды.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Обновите раздел `cloud_init_modules` в `/etc/cloud/cloud.cfg`, включив добавление следующих модулей:
```bash
- disk_setup
- mounts
```

В следующем примере показано, как должен выглядеть универсальный раздел `cloud_init_modules`.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
Также нужно обновить в `/etc/waagent.conf` ряд задач, связанных с инициализацией и обработкой временных дисков. Выполните следующие команды, чтобы применить соответствующие параметры. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Укажите Azure в качестве единственного источника данных агента Linux для Azure, создав в любом текстовом редакторе новый файл `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg`, содержащий следующие строки:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Добавьте конфигурацию для устранения ошибки невыполненной регистрации имени узла.
```bash
cat > /etc/cloud/hostnamectl-wrapper.sh <<\EOF
#!/bin/bash -e
if [[ -n $1 ]]; then
  hostnamectl set-hostname $1
else
  hostname
fi
EOF

chmod 0755 /etc/cloud/hostnamectl-wrapper.sh

cat > /etc/cloud/cloud.cfg.d/90-hostnamectl-workaround-azure.cfg <<EOF
# local fix to ensure hostname is registered
datasource:
  Azure:
    hostname_bounce:
      hostname_command: /etc/cloud/hostnamectl-wrapper.sh
EOF
```

Если в существующем образе Azure настроен файл подкачки, и вы хотите с помощью cloud-init изменить настройки файла подкачки для нового образа, необходимо удалить существующий файл подкачки.

Для образов на основе RedHat нужно выполнить инструкции по [удалению файла подкачки](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html), предоставленные в документации по RedHat.

Для образов на основе CentOS, в которых настроен файл подкачки, для его отключения можно выполнить следующую команду:
```bash
sudo swapoff /mnt/resource/swapfile
```

Обязательно удалите ссылку на файл подкачки из файла `/etc/fstab`, который должен выглядеть примерно так:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Чтобы освободить место и удалить файл подкачки, можно запустить следующую команду:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Дополнительный этап для образов, подготовленных с помощью cloud-init
> [!NOTE]
> Если используемый образ был ранее подготовлен и настроен с помощью **cloud-init**, необходимо сделать следующее.

Следующие три команды используются, только если вы хотите создать новый специализированный исходный образ из виртуальной машины, которая ранее была создана с помощью cloud-init.  Их НЕ СЛЕДУЕТ запускать, если для настройки образа применялся агент Linux для Azure.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Завершение настройки агента Linux 
Все образы платформы Azure содержат установленный агент Linux для Azure, даже если они были настроены с помощью cloud-init.  Запустите следующую команду, чтобы окончательно удалить этого пользователя с компьютера Linux. 

```bash
sudo waagent -deprovision+user -force
```

Дополнительные сведения о командах удаления агента Linux для Azure см. в статье [Агент Linux для Azure](agent-user-guide.md).

Завершите сеанс SSH, а затем выполните в оболочке bash следующие команды AzureCLI, которые освободят, обобщат и создадут новый образ виртуальной машины Azure.  Вместо `myResourceGroup` и `sourceVmName` укажите реальную информацию об исходной виртуальной машине.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные примеры изменения конфигурации с помощью cloud-init см. в следующих статьях:
 
- [Добавление пользователя Linux к виртуальной машине](cloudinit-add-user.md)
- [Запуск диспетчера пакетов для обновления существующих пакетов при первой загрузке](cloudinit-update-vm.md)
- [Изменение имени локального узла виртуальной машины](cloudinit-update-vm-hostname.md) 
- [Установка пакета приложения, обновление файлов конфигурации и внедрение ключей](tutorial-automate-vm-deployment.md)

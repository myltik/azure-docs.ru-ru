---
title: Обновление агента Linux для Azure из GitHub | Документация Майкрософт
description: Сведения об обновлении агента Linux для Azure на виртуальной машине Linux
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: danis
ms.openlocfilehash: b5a482ef6d15f1c6b1942a6128a807d7c6189918
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944966"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Как обновить агент Azure Linux на виртуальной машине

Для обновления [агента Linux для Azure](https://github.com/Azure/WALinuxAgent) на виртуальной машине Linux требуется:

- работающая виртуальная машина Linux в Azure;
- подключение к этой виртуальной машине Linux с помощью протокола SSH.

Пакет нужно всегда сначала проверять в репозитории дистрибутива Linux. Возможно, доступный пакет будет не последней версии. Тем не менее после включения автоматического обновления агент Linux будет всегда получать последнее обновление. При возникновении проблем во время установки из диспетчеров пакетов за поддержкой обратитесь к поставщику дистрибутива.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Минимальная поддержка агента виртуальных машин в Azure
Ознакомьтесь со статьей [Minimum version support for virtual machine agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Минимальная поддержка версий для агентов виртуальных машин в Azure) перед продолжением.

## <a name="updating-the-azure-linux-agent"></a>Обновление агента Linux для Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Обновление кэша пакета

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

#### <a name="restart-agent-for-1404"></a>Перезапуск агента для версии 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Перезапуск агента для версии 16.04, 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Обновление кэша пакета

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Включение автоматического обновления агента
В этой версии Debian нет версии агента не ниже 2.0.16, поэтому для нее недоступно автоматическое обновление. Проанализировав выходные данные команды выше, можно определить, обновлен ли пакет.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" и Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Обновление кэша пакета

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления 

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>RedHat или CentOS

### <a name="rhelcentos-6"></a>RHEL или CentOS 6

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Проверка доступных обновлений

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления 

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL или CentOS 7

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Проверка доступных обновлений

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления 

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Проверка доступных обновлений

Проанализировав выходные данные выше, можно определить, обновлен ли пакет.

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления 

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Проверка текущей версии пакета

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Проверка доступных обновлений

Проанализировав выходные данные выше, можно определить, обновлен ли пакет.

#### <a name="install-the-latest-package-version"></a>Установка последней версии пакета

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления 

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Перезапустите службу waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 и 7

При работе с Oracle Linux убедитесь, что включен репозиторий `Addons` . Откройте для редактирования файл `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) или `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) и замените в нем строку `enabled=0` на строку `enabled=1` в разделе **[ol6_addons]** или **[ol7_addons]**.

Затем введите следующие команды для установки последней версии агента Linux для Azure:

```bash
sudo yum install WALinuxAgent
```

Если вы не нашли репозиторий надстроек, можно просто добавить следующие строки в конец REPO-файла в соответствии с используемой версией Oracle Linux.

Для виртуальных машин Oracle Linux 6.

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Для виртуальных машин Oracle Linux 7.

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Затем введите следующую команду:

```bash
sudo yum update WALinuxAgent
```

Обычно это все, что требуется. Но если по какой-либо причине необходимо установить его с сайта https://github.com напрямую, сделайте следующее.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Обновление агента Linux, если пакет агента для дистрибутива не существует

Установите wget (некоторые дистрибутивы не устанавливают его по умолчанию, например Redhat, CentOS и Oracle Linux версий 6.4 и 6.5), введя `sudo yum install wget` в командной строке.

### <a name="1-download-the-latest-version"></a>1. Скачайте последнюю версию
Откройте [выпуск агента Linux для Azure в GitHub](https://github.com/Azure/WALinuxAgent/releases) на веб-странице и узнайте номер последней версии. (Номер текущей версии можно узнать, введя `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Для версии 2.2.x или более поздней версии введите:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

В следующей строке используется версия 2.2.0 в качестве примера:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Установка агента Linux для Azure

#### <a name="for-version-22x-use"></a>Для версии 2.2.x введите:
Возможно, сначала потребуется установить пакет `setuptools`. Ознакомьтесь со сведениями, приведенными [здесь](https://pypi.python.org/pypi/setuptools). Далее выполните:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Гарантия включения автоматического обновления

Сначала проверьте, включено ли автоматическое обновление:

```bash
cat /etc/waagent.conf
```

Найдите параметр AutoUpdate.Enabled. Если отображаются следующие результаты, значит эта возможность включена:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Чтобы включить автоматическое обновление, выполните следующую команду:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Перезапустите службу waagent
Для большинства дистрибутивов Linux:

```bash
sudo service waagent restart
```

Для Ubuntu используйте:

```bash
sudo service walinuxagent restart
```

Для CoreOS используйте:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Подтвердите версию агента Linux для Azure
    
```bash
waagent -version
```

Для CoreOS приведенная выше команда может не работать.

Вы увидите, что агент Linux для Azure обновлен до новой версии.

Дополнительные сведения об агенте Linux для Azure см. в [файле сведений агента Linux для Azure](https://github.com/Azure/WALinuxAgent).

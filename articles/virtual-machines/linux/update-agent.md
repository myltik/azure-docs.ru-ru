---
title: "Обновление агента Linux для Azure из GitHub | Документация Майкрософт"
description: "Узнайте, как обновить агент Linux для Azure на виртуальной машине Linux до последней версии с сайта GitHub."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c9cea69fc6462f69c8627219aca11272bbdc493f
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Как обновить агент Linux для Azure на виртуальной машине до последней версии с сайта GitHub
Для обновления [агента Linux для Azure](https://github.com/Azure/WALinuxAgent) на виртуальной машине Linux требуется:

1. работающая виртуальная машина Linux в Azure;
2. подключение к этой виртуальной машине Linux с помощью протокола SSH.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Если эта задача выполняется на компьютере Windows, можно использовать PuTTY, чтобы подключиться по протоколу SSH к компьютеру Linux. Дополнительные сведения см. в статье [Как войти в виртуальную машину под управлением Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Рекомендованные Azure дистрибутивы Linux содержат в своих репозиториях пакет агента Linux для Azure, так что сначала проверьте наличие и установите последнюю версию из этого репозитория дистрибутивов, если это возможно.  

Для Ubuntu просто введите:

```bash
sudo apt-get install walinuxagent
```

Для CentOS введите:

```bash
sudo yum install waagent
```

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
enabled=0
```

Затем введите следующую команду:

```bash
sudo yum update WALinuxAgent
```

Обычно это все, что требуется. Но если по какой-либо причине необходимо установить его с сайта https://github.com напрямую, сделайте следующее.

## <a name="install-wget"></a>Установка wget
Войдите на свою виртуальную машину с помощью SSH.

Установите wget (некоторые дистрибутивы не устанавливают его по умолчанию, например Redhat, CentOS и Oracle Linux версий 6.4 и 6.5), введя `#sudo yum install wget` в командной строке.

## <a name="download-the-latest-version"></a>Скачайте последнюю версию
Откройте [выпуск агента Linux для Azure в GitHub](https://github.com/Azure/WALinuxAgent/releases) на веб-странице и узнайте номер последней версии. (Номер текущей версии можно узнать, введя `#waagent --version`.)

### <a name="for-version-20x-type"></a>Для версии 2.0.x введите:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

В следующей строке используется версия 2.0.14 в качестве примера:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>Для версии 2.1.x или более поздней версии введите:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

В следующей строке используется версия 2.1.0 в качестве примера:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Установка агента Linux для Azure
### <a name="for-version-20x-use"></a>Для версии 2.0.x введите:
Сделайте waagent исполняемым:

```bash
chmod +x waagent
```

Скопируйте новый исполняемый файл в /usr/sbin/.

Для большинства дистрибутивов Linux используйте команды:

```bash
sudo cp waagent /usr/sbin
```

Для CoreOS используйте:

```bash
sudo cp waagent /usr/share/oem/bin/
```

Если это новая установка агента Linux для Azure, выполните следующую команду:

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>Для версии 2.1.x введите:
Возможно, сначала потребуется установить пакет `setuptools`. Ознакомьтесь со сведениями, приведенными [здесь](https://pypi.python.org/pypi/setuptools). Далее выполните:

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>Перезапустите службу waagent
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

## <a name="confirm-the-azure-linux-agent-version"></a>Подтвердите версию агента Linux для Azure
    
```bash
waagent -version
```

Для CoreOS приведенная выше команда может не работать.

Вы увидите, что агент Linux для Azure обновлен до новой версии.

Дополнительные сведения об агенте Linux для Azure см. в [файле сведений агента Linux для Azure](https://github.com/Azure/WALinuxAgent).



<properties
	pageTitle="Обновление агента Linux для Azure из GitHub | Microsoft Azure"
	description="Узнайте, как обновить агент Linux для Azure на виртуальной машине Linux до последней версии с сайта Github."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# Как обновить агент Linux для Azure на виртуальной машине до последней версии с сайта GitHub

Для обновления [агента Linux для Azure](https://github.com/Azure/WALinuxAgent) на виртуальной машине Linux требуется:

1. работающая виртуальная машина Linux в Azure;
2. подключение к этой виртуальной машине Linux с помощью протокола SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Если эта задача выполняется на компьютере Windows, можно использовать PuTTY, чтобы подключиться по протоколу SSH к компьютеру Linux. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Рекомендованные Azure дистрибутивы Linux содержат в своих репозиториях пакет агента Linux для Azure, так что сначала проверьте наличие и установите последнюю версию из этого репозитория дистрибутивов, если это возможно.

Для Ubuntu просто введите:

    #sudo apt-get install walinuxagent

Для CentOS введите:

    #sudo yum install waagent


При работе с Oracle Linux убедитесь, что включен репозиторий `Addons`. Откройте для редактирования файл `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) или `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) и замените в нем строку `enabled=0` на строку `enabled=1` в разделе **[ol6\_addons]** или **[ol7\_addons]**.

Затем введите следующие команды для установки последней версии агента Linux для Azure:


    #sudo yum install WALinuxAgent

Если вы не нашли репозиторий надстроек, можно просто добавить следующие строки в конец REPO-файла в соответствии с используемой версией Oracle Linux.

Для виртуальных машин Oracle Linux 6.

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Для виртуальных машин Oracle Linux 7.

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Затем введите следующую команду:

    #sudo yum update WALinuxAgent

Обычно это все, что требуется. Но если по какой-либо причине необходимо установить его из https://github.com напрямую, выполните следующие действия.


## Установка wget

Войдите на свою виртуальную машину с помощью SSH.

Установите wget (некоторые дистрибутивы не устанавливают его по умолчанию, например Redhat, CentOS и Oracle Linux версий 6.4 и 6.5), введя `#sudo yum install wget` в командной строке.


## Скачайте последнюю версию

Откройте [выпуск агента Linux для Azure в GitHub](https://github.com/Azure/WALinuxAgent/releases) на веб-странице и узнайте номер последней версии. (Номер текущей версии можно узнать, введя `#waagent --version`.)

### Для версии 2.0.x введите:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   В следующей строке используется версия 2.0.14 в качестве примера:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### Для версии 2.1.x или более поздней версии введите:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   В следующей строке используется версия 2.1.0 в качестве примера:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## Установка агента Linux для Azure

### Для версии 2.0.x введите:

 Сделайте waagent исполняемым:

    #chmod +x waagent

 Скопируйте новый исполняемый файл в /usr/sbin/.

  Для большинства дистрибутивов Linux используйте команды:

    #sudo cp waagent /usr/sbin

  Для CoreOS используйте:

    #sudo cp waagent /usr/share/oem/bin/

  Если это новая установка агента Linux для Azure, выполните следующую команду:
 
    #sudo /usr/sbin/waagent -install -verbose

### Для версии 2.1.x введите:

Возможно, сначала потребуется установить пакет `setuptools`. Ознакомьтесь с информацией, приведенной [здесь](https://pypi.python.org/pypi/setuptools). Далее выполните:

    #sudo python setup.py install

## Перезапустите службу waagent

Для большинства дистрибутивов Linux:

    #sudo service waagent restart

Для Ubuntu используйте:

    #sudo service walinuxagent restart

Для CoreOS используйте:

    #sudo systemctl restart waagent

## Подтвердите версию агента Linux для Azure

    #waagent -version

Для CoreOS приведенная выше команда может не работать.

Вы увидите, что агент Linux для Azure обновлен до новой версии.

Дополнительную информацию об агенте Linux для Azure см. в [файле сведений агента Linux для Azure](https://github.com/Azure/WALinuxAgent).

<!---HONumber=AcomDC_0824_2016-->
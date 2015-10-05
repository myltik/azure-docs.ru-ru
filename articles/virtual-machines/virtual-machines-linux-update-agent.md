<properties
	pageTitle="Обновление агента Linux для Azure из Github | Microsoft Azure"
	description="Узнайте, как обновить агент Linux для Azure на виртуальной машине Linux до последней версии с сайта Github."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Как обновить агент Linux для Azure на виртуальной машине до последней версии с сайта Github

Для обновления [агента Linux для Azure](https://github.com/Azure/WALinuxAgent) на виртуальной машине Linux требуется:

1. работающая виртуальная машина Linux в Azure;
2. подключение к этой виртуальной машине Linux с помощью протокола SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс управления ресурсом с помощью модели развертывания на основе диспетчера ресурсов, а также классической модели.

> [AZURE.NOTE]Если эта задача выполняется на компьютере Windows, можно использовать Putty, чтобы подключиться по протоколу SSH к компьютеру Linux. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).

Рекомендованные Azure дистрибутивы Linux содержат в своих репозиториях пакет агента Linux для Azure, так что сначала проверьте наличие и установите последнюю версию из этого репозитория дистрибутивов, если это возможно.

Для Ubuntu просто введите:

    #sudo apt-get install walinuxagent

Для CentOS введите:

    #sudo yum install waagent

Для Oracle Linux убедитесь, что дополнительный репозиторий включен в файле `/etc/yum.repo.d/public-yum-ol6.repo` или `/etc/yum.repo.d/public-yum-ol7.repo`, а затем введите:

    #sudo yum install WALinuxAgent

Обычно это все, что требуется. Но если по какой-либо причине необходимо установить его из https://github.com напрямую, выполните следующие действия.


## Установка wget

Войдите на свою виртуальную машину с помощью SSH.

Установите wget (некоторые дистрибутивы не устанавливают его по умолчанию, например Redhat, CentOS и Oracle Linux версий 6.4 и 6.5), введя `#sudo yum install wget` в командной строке.


## Скачивание последней версии

Откройте [выпуск агента Linux для Azure в Github](https://github.com/Azure/WALinuxAgent/releases) на веб-странице и узнайте номер последней версии. (Номер текущей версии можно узнать, введя `#waagent --version`.)

###Для версии 2.0.x введите:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   В следующей строке используется версия 2.0.14 в качестве примера:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###Для версии 2.1.x или более поздней введите:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   В следующей строке используется версия 2.1.0 в качестве примера:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Установка агента для Linux

###Для версии 2.0.x используйте:

 Создание исполняемого файла waagent

    #chmod +x waagent

 Копирование нового исполняемого файла в /usr/sbin/

  Для большинства дистрибутивов Linux используйте:

      #sudo cp waagent /usr/sbin

  Для CoreOS используйте:

    #sudo cp waagent /usr/share/oem/bin/

###Для версии 2.1.x используйте:

Возможно, сначала потребуется установить пакет `setuptools`. Ознакомьтесь с информацией [здесь](https://pypi.python.org/pypi/setuptools). Затем выполните следующее:

    #sudo python setup.py install

## Перезапуск службы waagent

Для большинства дистрибутивов Linux:

    #sudo service waagent restart

Для Ubuntu используйте:

    #sudo service walinuxagent restart

Для CoreOS используйте:

    #sudo systemctl restart waagent

## Подтверждение версии агента Azure Linux

    #waagent -version

Для CoreOS приведенная выше команда может не работать.

Вы увидите, что агент Linux обновлен до новой версии.

Дополнительную информацию об агенте Azure для Linux см. в [файле сведений агента Linux для Azure](https://github.com/Azure/WALinuxAgent).

<!---HONumber=Sept15_HO4-->
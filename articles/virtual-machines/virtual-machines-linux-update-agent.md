<properties
	pageTitle="Как обновить агент Linux для Azure до последней версии из Github"
	description="Узнайте, как обновить агент Linux для Azure из Github для виртуальной машины Linux в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Как обновить агент Linux для Azure до последней версии из Github

Для обновления [агента Azure Linux](https://github.com/Azure/WALinuxAgent) требуется:

1. работающая виртуальная машина Linux в Azure;
2. подключение к этой виртуальной машине Linux с помощью протокола SSH.

> [AZURE.NOTE]Если эта задача выполняется на компьютере Windows, можно использовать Putty, чтобы подключиться по протоколу SSH к компьютеру Linux. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).

Рекомендованные Azure дистрибутивы Linux содержат в своих репозиториях пакет агента Linux для Azure, так что сначала проверьте наличие и установите последнюю версию из этого репозитория дистрибутивов, если это возможно.

Для Ubuntu просто введите:
     
    #sudo apt-get install waagent

Для CentOS введите:

    #sudo yum install waagent

Для Oracle Linux убедитесь, что дополнительный репозиторий включен в файле `/etc/yum.repo.d/public-yum-ol6.repo` или `/etc/yum.repo.d/public-yum-ol7.repo`, а затем введите:

    #sudo yum install WALinuxAgent

Обычно это все, что требуется. Но если по какой-либо причине необходимо установить его из https://github.com напрямую, выполните следующие действия.


## Установка wget

Войдите на свою виртуальную машину с помощью SSH.

Установите wget (некоторые дистрибутивы не устанавливают его по умолчанию, например Redhat, CentOS и Oracle Linux версий 6.4 и 6.5), введя `#sudo yum install wget` в командной строке.


## Скачивание последней версии

Откройте [выпуск агента Linux для Azure в Github](https://github.com/Azure/WALinuxAgent/releases) на веб-странице и узнайте номер последней версии (например, 2.0.12). (Номер текущей версии можно узнать, введя `#waagent --version`.)

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

В следующей строке используется версия 2.0.12 в качестве примера:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## Создание исполняемого файла waagent

    #chmod +x waagent

## Копирование нового исполняемого файла в /usr/sbin/
    
    #sudo cp waagent /usr/sbin

Для CoreOS используйте:

    #sudo cp waagent /usr/share/oem/bin/
 
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



 

<!---HONumber=August15_HO6-->
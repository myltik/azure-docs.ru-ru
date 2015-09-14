<properties
	pageTitle="Использование удаленного рабочего стола через xrdp для подключения к виртуальной машине Microsoft Azure на базе Linux."
	description="Узнайте, как установить и настроить удаленный рабочий стол на виртуальной машине Microsoft Azure под управлением Linux."
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
	ms.date="08/31/2015"
	ms.author="mingzhan"/>


#Использование удаленного рабочего стола через xrdp для подключения к виртуальной машине Microsoft Azure на базе Linux

##Обзор

RDP (протокол удаленного рабочего стола) — это защищаемый протокол, используемый для Windows. Как же можно использовать RDP для удаленного подключения к виртуальной машине на базе Linux?

Ответ вы найдете в этом руководстве! Оно поможет установить и настроить xrdp на виртуальной машине Microsoft Azure под управлением Linux, а также подключиться к ней через удаленный рабочий стол с компьютера Windows.

Xrdp представляет собой сервер RDP с открытым исходным кодом, позволяющий подключиться к серверу на базе Linux с компьютера Windows через удаленный рабочий стол. Он выполняется гораздо лучше, чем VNC (Virtual Network Computing). VNC отличается проблемами с качеством, характерными для JPEG, и медленной работой, а RDP работает быстро и без проблем.
 

> [AZURE.NOTE]Вам необходима виртуальная машина Microsoft Azure под управлением Linux. Чтобы создать и настроить виртуальную машину на базе Linux, см. [руководство по виртуальным машинам Azure под управлением Linux](virtual-machines-linux-tutorial.md).


##Создание конечной точки для удаленного рабочего стола
Здесь мы будем использовать конечную точку по умолчанию 3389 для удаленного рабочего стола. Настройте конечную точку 3389 как удаленный рабочий стол для виртуальной машины Linux, как показано ниже:


![изображение](./media/virtual-machines-linux-remote-desktop/no1.png)


Если вы не знаете, как настроить конечную точку для виртуальной машины, см. [руководство](virtual-machines-set-up-endpoints.md).


##Установка Gnome Desktop

Подключитесь к виртуальной машине на базе Linux с помощью putty и установите `Gnome Desktop`.

Для семейства Red Hat Linux используйте следующую команду:

	#sudo yum install gnome* "xorg*" -y

Для Debian и Ubuntu используйте следующую команду:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Для OpenSUSE используйте следующую команду:

	#sudo zypper -y install gnome-session


##Установка xrdp

Для семейства Red Hat Linux следует сначала добавить репозиторий EPEL на виртуальной машине Linux, чтобы установить пакет xrdp через `yum`, используйте следующую команду:

	#sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	#sudo yum -y install xrdp tigervnc-server tigervnc-server-module xterm

Debian и Ubuntu Linux используйте следующую команду:

	#sudo apt-get install xrdp


Для OpenSUSE используйте следующую команду:

> [AZURE.NOTE]Укажите в приведенной ниже команде используемую вами версию OpenSUSE (пример приведен для `OpenSUSE 13.2`).

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Запуск xrdp и настройка запуска xdrp при загрузке системы

Для семейства Red Hat Linux используйте следующую команду:

	#sudo service xrdp start
	#sudo chkconfig xrdp on


Для OpenSUSE используйте следующую команду:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp
 

##Отключение iptables при использовании семейства Red Hat Linux 

Используйте следующую команду:

	#sudo service iptables stop


##Использование xfce при работе с версией Ubuntu, выпущенной после Ubuntu 12.04LTS

Поскольку текущий xrop не поддерживает Gnome Desktop из версии Ubuntu, выпущенной после Ubuntu 12.04LTS, мы будем использовать вместо этого `xfce` Desktop.

Установите `xfce`, используя следующую команду:

    #sudo apt-get install xubuntu-desktop

Затем включите `xfce`, используя следующую команду:
    
    #echo xfce4-session >~/.xsession

Измените файл конфигурации `/etc/xrdp/startwm.sh`, используя следующую команду:

    #sudo vi /etc/xrdp/startwm.sh   

Добавьте строку `xfce4-session` перед строкой `/etc/X11/Xsession`.

Перезапустите службу xrdp, используя следующую команду:

    #sudo service xrdp restart


##Подключение к виртуальной машине на базе Linux с компьютера Windows
На компьютере с ОС Windows запустите клиент удаленного рабочего стола, введите DNS-имя виртуальной машины на базе Linux или откройте `Dashboard` своей виртуальной машины на портале Azure и нажмите кнопку `Connect`, после чего отображается приведенное ниже окно входа:

![изображение](./media/virtual-machines-linux-remote-desktop/no2.png)

Войдите в систему, указав значения `user` и `password` для виртуальной машины на базе Linux, и сразу же приступайте к использованию удаленного рабочего стола из виртуальной машины Microsoft Azure на базе Linux!


##Далее
Дополнительные сведения об использовании xrdp см. [здесь](http://www.xrdp.org/).





 

<!---HONumber=September15_HO1-->
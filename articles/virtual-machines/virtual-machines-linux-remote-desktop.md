<properties
	pageTitle="Подключение к виртуальной машине под управлением Linux с помощью удаленного рабочего стола | Microsoft Azure"
	description="Узнайте, как установить и настроить удаленный рабочий стол для подключения к виртуальной машине Microsoft Azure под управлением Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="mingzhan"/>


#Использование удаленного рабочего стола для подключения к виртуальной машине Microsoft Azure на базе Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


##Обзор

RDP (протокол удаленного рабочего стола) — это защищаемый протокол, используемый для Windows. Как можно использовать протокол RDP для удаленного подключения к виртуальной машине под управлением Linux?

Ответ вы найдете в этом руководстве! Оно поможет установить и настроить xrdp на виртуальной машине Microsoft Azure на базе Linux, а также подключиться к ней с помощью удаленного рабочего стола на компьютере под управлением Windows. В качестве примера в этом руководстве используется виртуальная машина Linux под управлением Ubuntu или OpenSUSE.

Xrdp представляет собой сервер RDP с открытым исходным кодом, позволяющий подключиться к серверу на базе Linux с компьютера Windows через удаленный рабочий стол. Он выполняется гораздо лучше, чем VNC (Virtual Network Computing). VNC отличается проблемами с качеством, характерными для JPEG, и медленной работой, а RDP работает быстро и без проблем.


> [AZURE.NOTE]Вам необходима виртуальная машина Microsoft Azure под управлением Linux. Чтобы создать и настроить виртуальную машину на базе Linux, ознакомьтесь с [руководством по виртуальным машинам Azure под управлением Linux](virtual-machines-linux-tutorial.md).


##Создание конечной точки для удаленного рабочего стола
Здесь мы будем использовать конечную точку по умолчанию 3389 для удаленного рабочего стола. Настройте конечную точку 3389 как удаленный рабочий стол для виртуальной машины Linux, как показано ниже:


![изображение](./media/virtual-machines-linux-remote-desktop/no1.png)


Если вы не знаете, как настроить конечную точку для виртуальной машины, воспользуйтесь [руководством](virtual-machines-set-up-endpoints.md).


##Установка Gnome Desktop

Подключитесь к виртуальной машине на базе Linux с помощью putty и установите `Gnome Desktop`.

Для Ubuntu используйте:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Для OpenSUSE используйте следующую команду:

	#sudo zypper install gnome-session

##Установка xrdp

Для Ubuntu используйте:

	#sudo apt-get install xrdp

Для OpenSUSE используйте следующую команду:

> [AZURE.NOTE]Укажите в приведенной ниже команде используемую версию OpenSUSE (пример команды для `OpenSUSE 13.2` приведен ниже).

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Запуск xrdp и настройка запуска xdrp при загрузке системы

Для OpenSUSE используйте следующую команду:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

В Ubuntu после установки управляющей программы xrdp она автоматически запустится и включится при загрузке системы.

##Использование xfce при работе с версией Ubuntu, выпущенной после Ubuntu 12.04LTS

Так как текущая версия xrdp не поддерживает среду Gnome Desktop из версий Ubuntu, выпущенных после Ubuntu 12.04LTS, вместо нее мы будем использовать `xfce` Desktop.

Установите `xfce` с помощью следующей команды:

    #sudo apt-get install xubuntu-desktop

Затем включите `xfce` с помощью следующей команды:

    #echo xfce4-session >~/.xsession

Измените файл конфигурации `/etc/xrdp/startwm.sh`с помощью следующей команды:

    #sudo vi /etc/xrdp/startwm.sh   

Добавьте строку `xfce4-session` перед строкой `/etc/X11/Xsession`.

Перезапустите службу xrdp, используя следующую команду:

    #sudo service xrdp restart


##Подключение к виртуальной машине на базе Linux с компьютера Windows
На компьютере с Windows запустите клиент удаленного рабочего стола, введите DNS-имя виртуальной машины на базе Linux или откройте `Dashboard` своей виртуальной машины на классическом портале Azure и нажмите кнопку `Connect`, после чего отобразится следующее окно входа:

![изображение](./media/virtual-machines-linux-remote-desktop/no2.png)

Войдите в систему, указав значения `user` и `password` для виртуальной машины на базе Linux, и сразу же приступайте к использованию удаленного рабочего стола из виртуальной машины Microsoft Azure на базе Linux!


##Далее
Дополнительные сведения об использовании xrdp см. [здесь](http://www.xrdp.org/).

<!---HONumber=AcomDC_1203_2015-->
<properties 
	pageTitle="Создание и передача виртуального жесткого диска с операционной системой SUSE Linux в Azure" 
	description="Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему SUSE Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szarkos"/>


# Подготовка виртуальной машины SLES или openSUSE для Azure

- [Подготовка виртуальной машины SLES 11 с пакетом обновления 3 (SP3) для Azure](#sles11)
- [Подготовка виртуальной машины openSUSE версии 13.1 или более поздней для Azure](#osuse)

##Предварительные требования##

В этой статье предполагается, что вы уже установили операционную систему SUSE или openSUSE Linux на виртуальный жесткий диск. Существует несколько средств для создания VHD-файлов, например решение для виртуализации, такое как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).


**Замечания по установке SLES и openSUSE**

 - [SUSE Studio](http://www.susestudio.com) можно с легкостью использовать для создания образов SLES и openSUSE для Azure и Hyper-V, а также для управления этими образами. Этот подход рекомендуется для настройки собственных образов SUSE и openSUSE. Следующие официальные образы в коллекции SUSE Studio можно загрузить или клонировать для вашей собственной SUSE Studio:

  - [SLES 11 с пакетом обновления 3 (SP3) для Azure в коллекции SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [openSUSE 13.1 для Azure в коллекции SUSE Studio](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

- Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.

- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать LVM или [RAID](virtual-machines-linux-configure-raid.md).

- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.

- Все VHD-диски должны иметь размер, кратный 1 МБ.


## <a id="sles11"> </a>Подготовка SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3) ##

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3. Зарегистрируйте систему SUSE Linux Enterprise, чтобы позволить ей скачивать обновления и устанавливать пакеты.

4. Установите в системе последние исправления:

		# sudo zypper update

5. Установите агент Linux для Azure из репозитория SLES:

		# sudo zypper install WALinuxAgent

6. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл "/boot/grub/menu.lst" в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.

7.	Рекомендуется отредактировать файл /etc/sysconfig/network/dhcp и изменить параметр `DHCLIENT_SET_HOSTNAME` следующим образом:

		DHCLIENT_SET_HOSTNAME="no"

8.	В "/etc/sudoers" закомментируйте или удалите следующие строки, если они существуют:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию.

10.	Не создавайте пространство подкачки на диске с ОС

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


----------

## <a id="osuse"> </a>Подготовка openSUSE версии 13.1 или более поздней ##

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3. В консоли оболочки выполните команду `zypper lr`. Если эта команда возвращает результат аналогичный приведенному ниже (обратите внимание, что номера версий могут отличаться):

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	то репозитории настроены надлежащим образом, никаких изменений не требуется.

	Если команда возвращает сообщение "No repositories defined..." (Репозитории не определены...), используйте следующие команды для добавления этих репозиториев:

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1 
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	Чтобы убедиться, что репозитории добавлены, можно еще раз выполнить команду `zypper lr`. Если один из соответствующих репозиториев обновлений не включен, включите его, выполнив следующую команду:

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Обновите ядро до последней доступной версии:

		# sudo zypper up kernel-default

	Либо установите в системе все последние исправления:

		# sudo zypper update

5.	Установка агента Linux для Azure

		# sudo zypper install WALinuxAgent

6.	Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл "/boot/grub/menu.lst" в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Кроме того, удалите следующие параметры из строки загрузки ядра, если таковые существуют:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	Рекомендуется отредактировать файл /etc/sysconfig/network/dhcp и изменить параметр `DHCLIENT_SET_HOSTNAME` следующим образом:

		DHCLIENT_SET_HOSTNAME="no"

8.	**Внимание!** В файле /etc/sudoers закомментируйте или удалите следующие строки, если они существуют:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию.

10.	Не создавайте пространство подкачки на диске с ОС

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Убедитесь, что агент Linux для Azure запускается при загрузке:

		# sudo systemctl enable waagent.service

13. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


 

<!---HONumber=July15_HO1-->
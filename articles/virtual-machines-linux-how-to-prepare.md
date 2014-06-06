<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Подготовка дистрибутива" pageTitle="Подготовка дистрибутива Linux в Azure" metaKeywords="Azure Git CodePlex, веб-сайт Azure CodePlex, Azure веб-сайт Git" description="Узнайте, как использовать Git для публикации на веб-сайте Azure, а также как включить непрерывное развертывание из GitHub и CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Подготовка виртуальной машины Linux для Azure" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





# Подготовка виртуальной машины Linux для Azure 

Виртуальная машина в Azure работает под управлением операционной системы, выбранной при создании виртуальной машины. Azure хранит операционную систему виртуальной машины на виртуальном жестком диске в формате VHD (файл VHD). Виртуальный жесткий диск операционной системы, который был подготовлен к дублированию, называется образом. В этой статье показано, как создать собственный образ путем загрузки файла VHD с установленной и обобщенной операционной системой. Дополнительные сведения о дисках и образах в Azure см. в разделе [Управление дисками и образами](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj672979.aspx).

**Примечание**. При создании виртуальной машины можно настроить параметры операционной системы для оптимизации выполняемого приложения. Заданная конфигурация хранится на диске для данной виртуальной машины. Инструкции см. в разделе [Создание настраиваемой виртуальной машины](/ru-ru/manage/windows/how-to-guides/custom-create-a-vm/).

**Важно!** Соглашение об уровне обслуживания платформы Azure применяется к виртуальным машинам, работающим под управлением ОС Linux, только если один из встроенных дистрибутивов используется вместе со сведениями о конфигурации, указанными [в этой статье](http://support.microsoft.com/kb/2805216). Все дистрибутивы Linux, представленные в коллекции образов Azure, — это рекомендованные дистрибутивы, уже имеющие необходимую конфигурацию.


##Предварительные требования##
В данной статье предполагается, что у вас есть следующие элементы:

- **Сертификат управления** — Создан (и экспортирован в файл .cer) сертификат управления для подписки, для которой требуется загрузить VHD. Дополнительные сведения о создании сертификатов см. в разделе [Создание сертификата управления в Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/gg551722.aspx). 

- **Операционная система Linux установлена в VHD-файле.**  - Установлена поддерживаемая операционная система Linux на виртуальном жестком диске. Существует несколько средств для создания файлов .vhd. Для создания файла .vhd и установки операционной системы можно использовать решения для виртуализации, например Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/ru-ru/library/hh846766.aspx). 

	**Важно**! Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.

	Список рекомендованных дистрибутивов см. в документе [Linux в рекомендованных дистрибутивах Azure](../linux-endorsed-distributions). Кроме того, в разделе, приведенном в конце данной статьи, содержатся [сведения о нерекомендованных дистрибутивах][].

- **Средство командной строки Linux для Azure.** При использовании операционной системы Linux для создания образа следует использовать это средство для передачи VHD-файла. Чтобы загрузить это средство, ознакомьтесь с разделом [Средства командной строки Azure для Linux и Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409).

Командлет - **Add-AzureVHD**, который является частью модуля PowerShell Azure. Сведения о загрузке модуля см. в разделе [Загрузки Azure](/ru-ru/develop/downloads/). Сведения см. в разделе [Add-AzureVhd](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn205185.aspx).

Независимо от используемых дистрибутивов следует обратить внимание на следующее:

Агент Linux для Azure (Waagent) несовместим с NetworkManager. В конфигурации сети должен использоваться файл ifcfg-eth0, и конфигурация должна управляться через сценарии ifup/ifdown. Waagent не будет установлен, если обнаружен пакет NetworkManager.

NUMA не поддерживается из-за ошибки ядра Linux в версиях ниже 2.6.37. Установка waagent приведет к автоматическому отключению NUMA в конфигурации GRUB для командной строки ядра Linux.

Агент Linux для Azure нуждается в установке пакета python-pyasn1.

Рекомендуется не создавать раздел подкачки SWAP во время установки. Пространство SWAP можно настроить с помощью агента Linux для Azure. Также не рекомендуется использовать широко распространенное ядро Linux на виртуальной машине Azure без исправления, доступного на [веб-сайте Майкрософт](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409) (многие текущие дистрибутивы/ядра уже могут содержать это исправление).

Все VHD-диски должны иметь размер, кратный 1 МБ.

Эта задача включает следующие шаги:

- [Шаг 1. Подготовка образа для передачи] []
- [Шаг 2. Создание учетной записи хранения в Azure] []
- [Шаг 3. Подготовка подключения к Azure] []
- [Шаг 4. Передача образа в Azure] []

## <a id="prepimage"> </a>Шаг 1. Подготовка образа для загрузки ##

### Подготовка операционной системы CentOS 6.2+ ###

Необходимо выполнить определенные действия с конфигурацией операционной системы, чтобы виртуальная машина запускалась в среде Azure.

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3. Удалите NetworkManager, выполнив следующую команду:

		rpm -e --nodeps NetworkManager

	**Примечание.** Если пакет еще не установлен, эта команда завершится с сообщением об ошибке. Это ожидалось.

4.	Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:

		DEVICE=eth0
		ONBOOT=yes
		DHCP=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6. Включите сетевую службу, запустив приведенную ниже команду:

		chkconfig network on

7. CentOS 6.2 или 6.3. Установите драйверы для служб интеграции Linux

	**Примечание.** Это действие действительно только для CentOS 6.2 и 6.3.  В CentOS 6.4+ + службы интеграции Linux уже доступны в ядре.

	a) Загрузите ISO-файл, содержащий драйверы для служб интеграции Linux, из [Центра загрузки](http://www.microsoft.com/ru-ru/download/details.aspx?id=34603).

	b) На панели **Действия** диспетчера Hyper-V щелкните **Параметры**.

	![Откройте параметры Hyper-V](./media/virtual-machines-linux-how-to-prepare/settings.png)

	c) На панели **Оборудование** щелкните пункт **Контроллер IDE 1**.

	![Добавьте DVD-привод с установочным носителем](./media/virtual-machines-linux-how-to-prepare/installiso.png)

	d) В поле **Контроллер IDE** щелкните пункт **DVD-привод**, затем щелкните **Добавить**.

	e) Выберите **Файл образа**, перейдите к **Linux IC v3.2.iso** и нажмите **Открыть**.

	f) На странице **Параметры** нажмите кнопку **ОК**.

	g) Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

	h) Введите в окне командой строки следующие команды:

		mount /dev/cdrom /media
		/media/install.sh
		reboot

8. Установите python-pyasn1, выполнив следующую команду:

		sudo yum install python-pyasn1

9. Замените файл /etc/yum.repos.d/CentOS-Base.repo на следующий текст:

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10.	Добавьте следующие строки в /etc/yum.conf

		http_caching=packages
		exclude=kernel*

11. Отключите модуль yum "fastestmirror", открыв файл "/etc/yum/pluginconf.d/fastestmirror.conf" для редактирования и указав в разделе [main] следующую строку

		set enabled=0

12.	Выполните следующую команду для очистки текущих метаданных yum:

		yum clean all

13. В CentOS 6.2 и 6.3 обновите работающее ядро виртуальной машины, выполнив следующие команды

	В CentOS 6.2 выполните следующие команды:

		sudo yum remove kernel-firmware
		sudo yum --disableexcludes=all install kernel

	В CentOS 6.3+ введите следующую команду:

		sudo yum --disableexcludes=all install kernel

14. Измените строку загрузки ядра в grub для включения следующих параметров. Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Убедитесь, что все устройства SCSI, которые монтируются на уровне ядра, имеют время ожидания ввода/вывода не менее 300 секунд.

16.	В /etc/sudoers закомментируйте следующую строку, если она существует:

		Defaults targetpw

17.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.

18. Установите агент Linux для Azure, выполнив следующую команду

		yum install WALinuxAgent

19.	Не создавайте пространство подкачки на диске с ОС.

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure.  После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## ПРИМЕЧАНИЕ. Задайте для этого параметра нужное значение.

20.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

21. Щелкните в диспетчере Hyper-V пункт **Завершение работы**.


### Подготовка операционной системы Ubuntu 12.04+ ###

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3.	Замените текущие репозитории в образе, чтобы использовались репозитории Azure, где содержится ядро и пакет агента, необходимые для обновления ВМ.  Эти действия могут незначительно отличаться в зависимости от версии Ubuntu.

	Before editing /etc/apt/sources.list, it is recommended to make a backup
		sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		sudo apt-get update

	Ubuntu 12.10:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		sudo apt-get update

	Ubuntu 13.04+:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-get update

4. Обновите операционную систему до последней версии ядра, выполнив следующие команды: 

	Ubuntu 12.04:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

	Ubuntu 12.10:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot
	
	Ubuntu 13.04 и 13.10:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

5.	После системного сбоя ОС Ubuntu будет ожидать пользовательского ввода на экране загрузчика grub. Во избежание этого выполните следующие действия:

	a) Откройте файл /etc/grub.d/00_header.

	b) В функции **make_timeout()** найдите **if ["\${recordfail}" = 1 ]; затем**

	c) Измените инструкцию ниже этой строки на **set timeout=5**.

	d) Выполните команду update-grub.

6. Измените строку загрузки ядра в Grub или Grub2 для включения следующих параметров. Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.	В /etc/sudoers закомментируйте следующую строку, если она существует:

		Defaults targetpw

8.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.

9.	 Установите агент, выполнив следующие команды в режиме sudo:

		apt-get update
		apt-get install walinuxagent 

10.	Не создавайте пространство подкачки на диске с ОС.

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure.  После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## ПРИМЕЧАНИЕ. Задайте для этого параметра нужное значение.

11.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Щелкните в диспетчере Hyper-V пункт **Завершение работы**.


### Подготовка операционной системы SUSE Linux Enterprise Server 11 SP2 и SP3 ###

**ПРИМЕЧАНИЕ.** [SUSE Studio](http://www.susestudio.com) можно без труда использовать для создания образов SLES/opeSUSE для Azure и Hyper-V, а также для управления этими образами. Кроме того, в коллекции SUSE Studio доступны для загрузки приведенные ниже официальные образы, которые можно затем клонировать в свою учетную запись SUSE Studio для последующей настройки.

> - [SLES 11 SP2 для Azure в коллекции SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure)
> - [SLES 11 SP3 для Azure в коллекции SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3. Добавьте репозиторий, содержащий последнюю версию ядра и агента Linux для Azure. Выполните следующую команду: `zypper lr`. Например, в SLES 11 SP3 выход будет выглядеть следующим образом:

		# | Alias                        | Name               | Enabled | Refresh
		--+------------------------------+--------------------+---------+--------
		1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
		2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
		3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
		4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
		5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
		6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

 	В случае если команда возвращает сообщение об ошибке, например:

		"No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

	тогда, возможно, необходимо повторно включить репозитории или зарегистрировать систему.  Это можно сделать с помощью служебной программы suse_register.  Дополнительные сведения см. в [документации по SLES](https://www.suse.com/documentation/sles11/).

   Если один из соответствующих репозиториев обновлений не включен, включите его, выполнив следующую команду:

		zypper mr -e [REPOSITORY NUMBER]

   В приведенном выше случае фактической командой будет следующая:

		zypper mr -e 1 2 3 4

4. Обновите ядро до последней доступной версии:

		zypper up kernel-default

5. Установите агент Linux для Azure:

		zypper up WALinuxAgent
		
	Может появиться сообщение, аналогичное следующему:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor.
		Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

	Так как поставщик пакета был изменен с корпорации Майкрософт на "SUSE LINUX Products GmbH, Nuernberg, Germany", необходимо установить данный пакет, как указано в сообщении.

	Примечание. Версия пакета WALinuxAgent может немного отличаться.

6. Измените строку загрузки ядра в Grub для включения следующих параметров. Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.	Рекомендуется установить для параметра /etc/sysconfig/network/dhcp или его эквивалента значение с DHCLIENT_SET_HOSTNAME="yes" на DHCLIENT_SET_HOSTNAME="no".

8.	В /etc/sudoers закомментируйте следующую строку, если она существует:

		Defaults targetpw

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.

10.	Не создавайте пространство подкачки на диске с ОС.

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure.  После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## ПРИМЕЧАНИЕ. Задайте для этого параметра нужное значение.

11.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Щелкните в диспетчере Hyper-V пункт **Завершение работы**.


### Подготовка операционной системы openSUSE 12.3. ###

**ПРИМЕЧАНИЕ.** [SUSE Studio](http://www.susestudio.com) можно без труда использовать для создания образов SLES/opeSUSE для Azure и Hyper-V, а также для управления этими образами. Кроме того, в коллекции SUSE Studio доступны для загрузки приведенные ниже официальные образы, которые можно затем клонировать в свою учетную запись SUSE Studio для последующей настройки.

> - [openSUSE 12.3 для Azure в коллекции SUSE Studio](http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure)

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3. Обновите операционную систему до последней доступной версии ядра с использованием последних исправлений.

4. В консоли оболочки выполните команду `zypper lr`. Если эта команда возвращает

		# | Alias                     | Name                      | Enabled | Refresh
		--+---------------------------+---------------------------+---------+--------
		1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
		2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
		3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

   то репозитории настроены надлежащим образом, никаких изменений не требуется.

   Если команда возвращает "Репозитории не определены. Используйте команду 'zypper addrepo' для добавления одного или нескольких репозиториев", то необходимо повторно включить репозитории:

		zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
		zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

   Убедитесь, что репозитории были добавлены, снова выполнив команду 'zypper lr'.

   Если один из соответствующих репозиториев обновлений не включен, включите его, выполнив следующую команду:

		zypper mr -e [NUMBER OF REPOSITORY]

5.	Отключите автоматическую проверку DVD-диска.

6.	Установите агент Linux для Azure:

	Сначала добавьте репозиторий, содержащий новый WALinuxAgent:

		zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

	Затем выполните следующую команду:
 
		zypper up WALinuxAgent

	После запуска этой команды может появиться сообщение, аналогичное следующему:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
		Use 'zypper install WALinuxAgent' to install this candidate."

	Это сообщение является ожидаемым. Так как поставщик пакета был изменен с корпорации Майкрософт на "obs://build.opensuse.org/Cloud," необходимо установить данный пакет, как указано в сообщении.

7.	Измените строку загрузки ядра в Grub для включения следующих параметров. Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

 	Кроме того, в /boot/grub/menu.lst удалите следующие параметры из командной строки ядра, если таковые существуют:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

9.	Рекомендуется установить для параметра /etc/sysconfig/network/dhcp или его эквивалента значение с DHCLIENT_SET_HOSTNAME="yes" на DHCLIENT_SET_HOSTNAME="no".

10.	В /etc/sudoers закомментируйте следующую строку, если она существует:

		Defaults targetpw

11.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.

12.	Не создавайте пространство подкачки на диске с ОС.

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure.  После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## ПРИМЕЧАНИЕ. Задайте для этого параметра нужное значение.

13.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

14. Убедитесь, что агент Linux для Azure запускается при загрузке.

		systemctl enable waagent.service

14. Щелкните в диспетчере Hyper-V пункт **Завершение работы**.


## <a id="createstorage"> </a>Шаг 2. Создание учетной записи хранения в Azure ##

Учетная запись хранения представляет собой высший уровень пространства имен для доступа к службам хранения; эта запись связана с вашей подпиской Azure. Для загрузки в Azure файла .vhd, который можно использовать для создания виртуальной машины, потребуется учетная запись хранения в Azure. Можно создать учетную запись хранения с помощью портала управления Azure.

1. Выполните вход на портал управления Azure.

2. На панели команд щелкните **Создать**.

	![Создание учетной записи хранения](./media/virtual-machines-linux-how-to-prepare/create.png)

3. Щелкните **Учетная запись хранения**, а затем **Быстрое создание**.

	![Быстрое создание учетной записи хранения](./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png)

4. Заполните поля, как показано ниже:

	![Введите сведения об учетной записи хранения](./media/virtual-machines-linux-how-to-prepare/storage-create-account.png)

- В поле **URL-адрес** введите имя дочернего домена, используемое в URL для учетной записи хранения. Записи могут содержать от 3 до 24 строчных букв и цифр. Это имя становится именем узла в URL, который используется для адресации ресурсов BLOB-объекта, очереди и таблицы для подписки.
	
- Выберите расположение или территориальную группу для учетной записи хранения. Указав территориальную группу, можно выполнить совместное размещение облачных служб в одном центре обработки данных с хранилищем.
 
- Укажите, следует ли использовать георепликацию для учетной записи хранения. По умолчанию георепликация включена. Этот параметр позволяет бесплатно выполнять репликацию данных в дополнительное местоположение; таким образом в случае аварийного отказа, который не может быть устранен в основном местоположении, хранилище переключится на дополнительное местоположение. Дополнительное местоположение назначается автоматически и не может быть изменено. Если в соответствии с законодательными требованиями или организационной политикой требуется более жесткий контроль местоположения облачного хранилища, то георепликацию можно отключить. Однако следует помнить, что если вы опять включите георепликацию, с вас будет взята однократная плата за репликацию существующих данных в дополнительное местоположение. Службы хранения без георепликации предлагаются со скидкой.

5. Щелкните **Создать учетную запись хранения**.

	Учетная запись появится в списке **Учетные записи хранения**.

	![Учетная запись хранилища успешно создана](./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png)


## <a id="#connect"> </a>Шаг 3. Подготовка подключения к Azure ##

Перед загрузкой файла .vhd необходимо установить безопасное соединение между компьютером и вашей подпиской в Azure. 

1. Откройте окно PowerShell для Azure.

2. Введите: 

	`Get-AzurePublishSettingsFile`

	Эта команда открывает окно браузера и автоматически загружает файл .publishsettings, содержащий информацию и сертификат для подписки Azure. 

3. Сохраните файл .publishsettings. 

4. Введите:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Где `<PathToFile>` указывает полный путь к файлу .publishsettings. 

	Дополнительные сведения см. в разделе [Начало работы с командлетами Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554332.aspx). 


## <a id="upload"> </a>Шаг 4. Передача образа в Azure ##

При загрузке файла .vhd файл .vhd можно поместить в любом месте внутри хранилища blob. В приведенных ниже примерах команд **BlobStorageURL** является URL-адресом учетной записи хранения, которая была создана на шаге 2, **YourImagesFolder** является контейнером в хранилище BLOB-объектов, где необходимо сохранить образы. **VHDName** является меткой, которая отображается на портале управления для идентификации виртуального диска. **PathToVHDFile** является полным путем и именем файла .vhd. 

Выполните одно из следующих действий.

- В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Дополнительные сведения см. в разделе [Add-AzureVhd](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn205185.aspx).

- Используйте средство командной строки Linux для передачи образа. Можно передать образ, используя следующую команду:

		Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <a id="nonendorsed"> </a>Сведения по нерекомендованным дистрибутивам ##
По существу все дистрибутивы, работающие в Azure, должны соответствовать следующим предварительным требованиям для правильной работы на платформе. 

Этот список далеко не исчерпывающий, так как каждый дистрибутив имеет свои отличия; вполне возможно, что даже при соблюдении всех изложенных ниже критериев, необходимо будет в значительной мере настраивать используемый образ для его надлежащей работы на платформе.

Именно по этой причине мы рекомендуем начать с одного из [рекомендованных образов наших партнеров](https://www.windowsazure.com/ru-ru/manage/linux/other-resources/endorsed-distributions/).

В следующем списке заменяется шаг 1 процесса для создания собственного VHD:

1.	Необходимо убедиться, что либо запущено ядро, которое включает в себя последние версии драйверов LIS для Hyper-V, либо эти драйверы были успешно скомпилированы (они имеют открытый исходный код). Драйверы можно найти [в этом месте](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409).

2.	Ядро также должно включать последнюю версию драйвера ATA PiiX, которая используется для подготовки образов и содержит исправления ядра cd006086fa5d91414d8ff9ff2b78fbb593878e3c Дата: пятница, 4 мая 22:15:11 2012 + 0100 ata_piix: предоставить диски драйверам Hyper-V по умолчанию.

3.	Сжатый intird должен быть меньше 40 МБ (* мы постоянно работаем для увеличения этого значения, поэтому может быть, что оно уже устарело).

4.	Измените строку загрузки ядра в Grub или Grub2 для включения следующих параметров. Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.	Рекомендуется установить для параметра /etc/sysconfig/network/dhcp или его эквивалента значение с DHCLIENT_SET_HOSTNAME="yes" на DHCLIENT_SET_HOSTNAME="no".

6.	Убедитесь, что все устройства SCSI, которые монтируются на уровне ядра, имеют время ожидания ввода/вывода не менее 300 секунд.

7.	Необходимо установить агент Linux для Azure, следуя шагам, приведенным в [руководстве по агенту Linux](https://www.windowsazure.com/ru-ru/manage/linux/how-to-guides/linux-agent-guide/). Агент был выпущен по лицензии Apache 2, и вы можете получить последнюю рабочую версию в [расположении GitHub агента](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409).

8.	В /etc/sudoers закомментируйте следующую строку, если она существует:

		Defaults targetpw

9.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.

10.	Не создавайте пространство подкачки на диске с ОС.

	Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure.  После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## ПРИМЕЧАНИЕ. Задайте для этого параметра нужное значение.

11.	Вам необходимо выполнить следующие команды для отмены подготовки виртуальной машины:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12.	Затем необходимо завершить работу виртуальной машины и выполнить передачу.


[Шаг 1. Подготовка образа для передачи]: #prepimage
[Шаг 2. Создание учетной записи хранения в Azure]: #createstorage
[Шаг 3. Подготовка подключения к Azure]: #connect
[Шаг 4. Передача образа в Azure]: #upload
[сведения о нерекомендованных дистрибутивах]: #nonendorsed


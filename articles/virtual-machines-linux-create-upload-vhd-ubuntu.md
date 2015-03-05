<properties 
	pageTitle="Создание и передача виртуального жесткого диска с ОС Ubuntu Linux в Azure" 
	description="Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему Ubuntu Linux." 
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
	ms.date="01/13/2015" 
	ms.author="szarkos"/>


# Подготовка виртуальной машины Ubuntu для Azure

##Предварительные требования##

В этой статье предполагается, что вы уже установили операционную систему Ubuntu Linux на виртуальный жесткий диск. Для создания VHD-файлов существует несколько инструментов, например решение виртуализации, такое как Hyper-V. Указания см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx). 

**Замечания по установке Ubuntu**

- Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.

- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок.  При желании LVM или [RAID](../virtual-machines-linux-configure-raid) может использоваться на дисках данных.

- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны далее.

- Все VHD-диски должны иметь размер, кратный 1 МБ.


## <a id="ubuntu"> </a>Ubuntu 12.04+ ##

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно виртуальной машины.

3.	Замените текущие репозитории в образе на репозитории Ubuntu Azure. Эти действия могут незначительно отличаться в зависимости от версии Ubuntu.

	Перед редактированием файла /etc/apt/sources.list рекомендуется сделать резервную копию

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14.04+:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. Обновите операционную систему до последней версии ядра, выполнив следующие команды: 

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot
	
	Ubuntu 14.04+:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(дополнительно) Если система Ubuntu обнаруживает ошибку и перезагружается, она будет ожидать пользовательского ввода на экране загрузчика grub, что предотвращает правильную загрузку системы. Во избежание этого выполните следующие действия:

	a) откройте файл /etc/grub.d/00_header;

	b) В функции **make_timeout()** найдите **if ["\${recordfail}" = 1 ]; then** и

	c) измените оператор ниже этой строки на **set timeout=5**;

	d) Запустите 'sudo update-grub'.

6. Измените строку загрузки ядра в конфигурации Grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл /etc/default/grub в текстовом редакторе, найдите переменную `GRUB_CMDLINE_LINUX_DEFAULT` (или добавьте ее, если это необходимо) и измените эту переменную, включив в нее следующие параметры:

		GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	Сохраните и закройте этот файл, затем выполните "`sudo update-grub`". Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. 

8.	Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.

9.	Установите агент Linux для Azure:

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	Обратите внимание, что установка пакета `walinuxagent` приведет к удалению пакетов `NetworkManager` и `NetworkManager-gnome`, если таковые установлены.

10.	Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. В диспетчере Hyper-V выберите **Действие > Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.



<!--HONumber=45--> 

.<properties
	pageTitle="Подготовка виртуального жесткого диска Debian Linux | Microsoft Azure"
	description="Дополнительные сведения о создании VHD-файлов Debian 7 и 8 для развертывания в Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>



# Подготовка виртуального жесткого диска Debian для Azure

## Предварительные требования
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) операционную систему Debian Linux из ISO-файла, полученного с [веб-сайта Debian](https://www.debian.org/distrib/). Существует несколько средств для создания VHD-файлов. Hyper-V — это лишь один из примеров. Инструкции по работе с Hyper-V см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](https://technet.microsoft.com/library/hh846766.aspx).


## Замечания по установке

- Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes).
- Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета **convert-vhd**.
- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. При желании на дисках данных можно использовать [LVM](virtual-machines-linux-configure-lvm.md) или [RAID](virtual-machines-linux-configure-raid.md).
- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для Azure для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.
- Все VHD-диски должны иметь размер, кратный 1 МБ.


## Создание виртуальных жестких дисков Debian с помощью Azure-Manage

Создавать виртуальные жесткие диски Debian для Azure можно с помощью сценариев [azure-manage](https://github.com/credativ/azure-manage) от компании [credativ](http://www.credativ.com/). Рекомендуется использовать их, а не создавать образ с нуля. Например, чтобы создать виртуальный жесткий диск Debian 8, выполните следующие команды для скачивания azure-manage (и зависимостей) и выполнения скрипта azure\_build\_image:

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://github.com/credativ/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## Подготовка виртуального жесткого диска Debian вручную

1. В диспетчере Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно консоли для виртуальной машины.

3. Закомментируйте строку **deb cdrom** в `/etc/apt/source.list`, если вы настроили виртуальную машину в соответствии с ISO-файлом.

4. Отредактируйте файл `/etc/default/grub` и измените параметр **GRUB\_CMDLINE\_LINUX** следующим образом, чтобы включить дополнительные параметры ядра для Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=30"

5. Перестройте grub и выполните команду:

        # sudo update-grub

6. Добавьте репозитории Azure Debian в файл /etc/apt/sources.list для Debian 7 или 8.

	**Debian 7.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 8.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Установите агент Linux для Azure:

		# sudo apt-get update
		# sudo apt-get install waagent

8. Для Debian 7 необходимо запустить ядро на основе версии 3.16 из репозитория wheezy-backports. Сначала создайте файл с именем /etc/apt/preferences.d/linux.pref со следующим содержимым:

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	Затем выполните команду "sudo apt-get install linux-image-amd64", чтобы установить новое ядро.

8. Отмените подготовку виртуальной машины, подготовьте ее к работе в среде Azure и выполните команду:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


## Дальнейшие действия

Теперь виртуальный жесткий диск Debian можно использовать для создания новых виртуальных машин Azure. Если вы впервые отправляете VHD-файл в Azure, см. шаги 2 и 3 в статье [Создание и передача виртуального жесткого диска с операционной системой Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0831_2016-->
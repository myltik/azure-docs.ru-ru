<properties
	pageTitle="Подготовка виртуального жесткого диска Debian Linux | Microsoft Azure"
	description="Дополнительные сведения о создании VHD-файлов Debian 7 и 8 для развертывания в Azure."
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
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Подготовка виртуального жесткого диска Debian для Azure

##Предварительные требования
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) операционную систему Debian Linux из ISO-файла, полученного с [веб-сайта Debian](https://www.debian.org/distrib/). Существует несколько средств для создания VHD-файлов. Hyper-V — это лишь один из примеров. Инструкции по работе с Hyper-V см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](https://technet.microsoft.com/library/hh846766.aspx).


## Замечания по установке

- Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета **convert-vhd**.
- При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. При желании LVM или RAID может использоваться на дисках данных.
- Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для Azure для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.
- Все VHD-диски должны иметь размер, кратный 1 МБ.


##Debian 7.x и 8.x

1. В диспетчере Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение**, чтобы открыть окно консоли для виртуальной машины.

3. Закомментируйте строку **deb cdrom** в `/etc/apt/source.list`, если вы настроили виртуальную машину в соответствии с ISO-файлом.

4. Отредактируйте файл `/etc/default/grub` и измените параметр **GRUB\_CMDLINE\_LINUX** следующим образом, чтобы включить дополнительные параметры ядра для Azure.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Перестройте grub и выполните команду:

        # sudo update-grub 

6. Установите пакеты зависимостей для агента Linux для Azure:

        # apt-get install -y git parted

7.	Установите агенты Linux для Azure с сайта Github, используя [руководство](virtual-machines-linux-update-agent.md), и выберите версию 2.0.14:

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Отмените подготовку виртуальной машины, подготовьте ее к работе в среде Azure и выполните команду:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

##Использование сценария Credativ для создания виртуального жесткого диска Debian

На веб-сайте Credativ есть сценарий, который позволяет автоматически создавать виртуальный жесткий диск Debian. Его можно скачать [здесь](https://gitlab.credativ.com/de/azure-manage) и установить на виртуальной машине Linux. Чтобы создать виртуальный жесткий диск Debian (например Debian 7), выполните команду:

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Если при использовании этого сценария возникнут проблемы, сообщите о проблеме в Credativ [здесь](https://gitlab.credativ.com/groups/de/issues).

## Дальнейшие действия

Теперь вы можете использовать виртуальный жесткий диск Debian для создания новых виртуальных машин Azure. Если вы используете Azure и отправляете VHD-файл в Azure в первый раз, следуйте инструкциям, описанным в шагах 2 и 3 [этого руководства](virtual-machines-linux-create-upload-vhd.md).

<!---HONumber=AcomDC_0121_2016-->
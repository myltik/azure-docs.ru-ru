<properties
	pageTitle="Создание виртуальной машины Azure, работающей под управлением ОС Linux, на классическом портале Azure | Microsoft Azure"
	description="C помощью классического портала Azure можно создать виртуальную машину Azure, работающую под управлением ОС Linux, с группами ресурсов Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Создание виртуальной машины, работающей под управлением ОС Linux, на портале Azure

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

<br>


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Создать виртуальную машину Azure, работающую под управлением ОС Linux, несложно. Из данного руководства вы узнаете, как быстро создать такую виртуальную машину с помощью портала Azure и как использовать файл открытого ключа `~/.ssh/id_rsa.pub` для защиты подключения к виртуальной машине по **SSH**. Вы также можете создавать виртуальные машины Linux, используя [свои собственные образы в качестве шаблонов](virtual-machines-linux-create-upload-vhd.md).

> [AZURE.NOTE]В данном учебнике показано, как создать виртуальную машину Azure, управляемую с помощью API группы ресурсов Azure. Дополнительные сведения см. в [обзоре групп ресурсов Azure](resource-group-overview.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Выбор образа

Перейдите в Azure Marketplace на портале предварительной версии, чтобы найти необходимый образ виртуальной машины Windows Server.

1. Войдите на [портал предварительной версии](https://portal.azure.com).

2. В главном меню последовательно выберите пункты **Создать**, **Среда выполнения приложений**, **Ubuntu Server 14.04 LTS**.

	![выбор образа виртуальной машины](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP]Чтобы найти дополнительные образы, выберите пункт **Marketplace**, а затем выполните поиск среди доступных элементов или отфильтруйте их список необходимым образом.

3. Чтобы создать виртуальную машину в диспетчере ресурсов Azure, в нижней части страницы **Ubuntu Server 14.04 LTS** выберите пункт **Использовать стек диспетчера ресурсов**. Обратите внимание, что для большинства новых рабочих нагрузок рекомендуется использовать стек диспетчера ресурсов. Дополнительные сведения см. в статье [Поставщики вычислительных и сетевых ресурсов, а также ресурсов службы хранилища Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md).

4. Затем нажмите кнопку ![кнопка "Создать"](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![изменения стека вычислений диспетчера ресурсов](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## Создание виртуальной машины

Выбрав образ, для большей части параметров в конфигурации можно оставить значения, используемые Azure по умолчанию, и быстро создать виртуальную машину.

1. В колонке **Создание виртуальной машины** щелкните **Основные сведения**. Укажите **имя** виртуальной машины и файл открытого ключа (в формате **ssh-rsa**; в данном случае это файл `~/.ssh/id_rsa.pub`). Если у вас несколько подписок, укажите одну из них для новой виртуальной машины, а также новую или существующую **группу ресурсов** и **расположение** центра обработки данных Azure.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE]Кроме того, здесь можно выбрать функцию аутентификации по имени пользователя и паролю и указать их, если вы не хотите использовать защиту сеанса **SSH** с помощью обмена открытым и закрытым ключами.

2. Щелкните **Размер** и укажите необходимый размер виртуальной машины. Каждому размеру соответствуют определенные количество вычислительных ядер, объем памяти и другие функции, например поддержка хранилища класса Premium, что влияет на цену. В зависимости от выбранного образа Azure автоматически рекомендует определенные размеры. По завершении нажмите кнопку ![кнопка "Выбрать"](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png).

	>[AZURE.NOTE]Хранилище класса Premium доступно для виртуальных машин серии DS в определенных регионах. Хранилище класса Premium — наилучший вариант хранения данных для рабочих нагрузок с интенсивным обменом данными, например для базы данных. Дополнительные сведения см. в статье [Хранилище Premium: хранилище высокой производительности для рабочих нагрузок виртуальных машин Azure](storage-premium-storage-preview-portal.md).

3. Щелкните **Параметры**, чтобы отобразить параметры хранилища и сети для новой виртуальной машины. Для первой виртуальной машины можно в целом принять значения параметров по умолчанию. Если вы выбрали размер виртуальной машины, который поддерживает хранилище класса Premium, то можно опробовать его, выбрав значение **Premium (SSD)** для параметра **Тип диска**. По завершении нажмите кнопку ![кнопка ОК](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png).

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. Щелкните **Сводка**, чтобы отобразить выбранные вами значения параметров. После проверки или изменения параметров щелкните ![кнопка ОК](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![сводка по созданию](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. Процесс создания виртуальной машины в Azure можно отслеживать в разделе **Уведомления** в главном меню. После создания виртуальной машины в Azure она отобразится на начальной панели, если только не снят флажок **Закрепить на начальной панели** в колонке **Создание виртуальной машины**.

	> [AZURE.NOTE]Обратите внимание, что в сводке нет общедоступного DNS-имени, которое обычно отображается при создании виртуальной машины в облачной службе с помощью стека вычислений портала управления службами.

## Подключение к виртуальной машине Linux в Azure c помощью протокола **SSH**

Теперь можно обычным способом подключиться к виртуальной машине Ubuntu с помощью протокола **SSH**. Вам необходимо обнаружить IP-адрес, выделенный виртуальной машине Azure, открыв плитку для виртуальной машины и ее ресурсов. Чтобы сделать это, щелкните **Обзор**, выберите пункт **Последние** и найдите созданную вами виртуальную машину либо щелкните элемент, созданный на начальной панели. В любом случае необходимо найти и скопировать значение **общедоступного IP-адреса**, показанного на рисунке ниже.

![сводка по успешному созданию](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

Теперь с помощью протокола **SSH** можно войти на виртуальную машину Azure и начать работу.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


> [AZURE.NOTE]Кроме того, вы можете настроить полное доменное имя (FQDN) для своей виртуальной машины на портале. Узнайте больше о [создании полных доменных имен на портале](virtual-machines-create-fqdn-on-portal.md).

## Дальнейшие действия

Чтобы узнать больше о Linux в Azure, см. следующие статьи:

- [Linux и вычисления с открытым кодом в Azure](virtual-machines-linux-opensource.md)

- [Средства командной строки Azure для Mac и Linux](virtual-machines-command-line-tools.md)

- [Развертывание приложения LAMP с помощью расширения Azure CustomScript для Linux](virtual-machines-linux-script-lamp.md)

- [Расширение виртуальных машин Docker для Linux в Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=AcomDC_0114_2016-->
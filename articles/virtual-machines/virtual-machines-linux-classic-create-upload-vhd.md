<properties
	pageTitle="Создание и передача виртуального жесткого диска Linux | Microsoft Azure"
	description="Создание и передача виртуального жесткого диска Azure, содержащего операционную систему Linux, с использованием классической модели развертывания."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Создание и передача виртуального жесткого диска с операционной системой Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


В этой статье показано, как создать и передать виртуальный жесткий диск (VHD-файл), чтобы использовать его в качестве образа для создания виртуальных машин в Azure. Вы узнаете, как подготовить операционную систему, чтобы использовать ее в качестве образа для создания нескольких виртуальных машин.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Виртуальная машина Azure запускает операционную систему на основе образа, выбранного во время создания. Эти образы хранятся в формате виртуальных жестких дисков (VHD-файлы) в учетной записи хранения. Дополнительные сведения см. в статьях [Диски в Azure](virtual-machines-linux-about-disks-vhds.md) и [Образы в Azure](virtual-machines-linux-classic-about-images.md).


При создании виртуальной машины можно настроить некоторые параметры операционной системы, чтобы оптимизировать их под приложение, которое вы хотите запустить. Инструкции см. в разделе [Создание настраиваемой виртуальной машины](virtual-machines-windows-classic-createportal.md).

**Важно!** Соглашение об уровне обслуживания платформы Azure применяется к виртуальным машинам, работающим под управлением ОС Linux, только если один из рекомендуемых дистрибутивов используется вместе со сведениями о конфигурации, указанными в статье «Поддерживаемые версии» раздела [Linux с рекомендуемыми дистрибутивами Azure](virtual-machines-linux-endorsed-distros.md). Все дистрибутивы Linux в коллекции образов Azure — это рекомендуемые дистрибутивы, уже имеющие необходимую конфигурацию.


## Предварительные требования
В данной статье предполагается, что у вас есть следующие элементы:

- **Сертификат управления**. Создан (и экспортирован в файл .cer) сертификат управления для подписки, для которой требуется загрузить VHD. Дополнительные сведения о создании сертификатов см. в статье [Общие сведения о сертификатах для Azure](../cloud-services/cloud-services-certs-create.md).

- **Операционная система Linux установлена в файле VHD**. Вы установили поддерживаемую операционную систему Linux на виртуальный жесткий диск. Существует несколько средств для создания файлов VHD. Например, для создания файла VHD и установки операционной системы можно использовать решения для виртуализации, такие как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

	**Важно!** Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.

	Список рекомендованных дистрибутивов см. в документе [Linux в рекомендованных дистрибутивах Azure](virtual-machines-linux-endorsed-distros.md). Общий список дистрибутивов Linux см. в статье [Информация о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md).


- **Интерфейс командной строки Azure**. При использовании операционной системы Linux для создания образа следует использовать [интерфейс командной строки Azure](../virtual-machines-command-line-tools.md) для передачи VHD-файла.

- **Инструменты Azure Powershell**. Командлет `Add-AzureVhd` также можно использовать для передачи VHD-файла. Чтобы скачать командлеты Azure Powershell, перейдите на страницу [Загрузки Azure](https://azure.microsoft.com/downloads/). Справочные сведения см. в разделе [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

<a id="prepimage"> </a>
## Шаг 1. Подготовка образа для передачи

Microsoft Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](virtual-machines-linux-endorsed-distros.md)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure:

- **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES и openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-generic.md)**

Дополнительные советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#linuxinstall)**.

После выполнения этапов, описанных в вышеуказанных руководствах, у вас должен быть VHD-файл для передачи в Azure.

<a id="connect"> </a>
## Шаг 2. Подготовка подключения к Azure

Перед загрузкой файла VHD необходимо установить безопасное соединение между компьютером и вашей подпиской в Azure.


### При использовании CLI Azure

Последняя версия CLI Azure включается в модель развертывания диспетчера ресурсов по умолчанию, поэтому убедитесь, что вы находитесь в классической модели развертывания, выполнив следующую команду:

		azure config mode asm  

Затем войдите в систему для подключения к подписке Azure любым из описанных ниже способов.

Используйте метод Azure AD для входа:

1. Откройте окно CLI Azure.

2. Введите:

	`azure login`

	При появлении запроса введите имя пользователя и пароль.

**ИЛИ** вместо этого используйте PUBLISHSETTINGS-файл.

1. Откройте окно CLI Azure.

2. Введите:

	`azure account download`

	Эта команда открывает окно браузера и автоматически загружает файл .publishsettings, содержащий информацию и сертификат для подписки Azure.

3. Сохраните файл .publishsettings.

4. Тип:

	`azure account import <PathToFile>`

	`<PathToFile>` — это полный путь к файлу PUBLISHSETTINGS.

	Дополнительную информацию см. в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).


### При использовании Azure PowerShell

Используйте метод Azure AD для входа:

1. Откройте окно Azure PowerShell.

2. Тип:

	`Add-AzureAccount`

	При появлении запроса введите идентификатор организации пользователя и пароль.

**ИЛИ** вместо этого используйте PUBLISHSETTINGS-файл.

1. Откройте окно Azure PowerShell.

2. Тип:

	`Get-AzurePublishSettingsFile`

	Эта команда открывает окно браузера и автоматически загружает файл .publishsettings, содержащий информацию и сертификат для подписки Azure.

3. Сохраните файл .publishsettings.

4. Тип:

	`Import-AzurePublishSettingsFile <PathToFile>`

	`<PathToFile>` — это полный путь к файлу PUBLISHSETTINGS.

	Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md)

> [AZURE.NOTE] Мы советуем использовать новой метод Azure Active Directory для входа в подписку Azure с помощью интерфейса командной строки Azure или Azure PowerShell.

<a id="upload"> </a>
## Шаг 3. Передача образа в Azure

Потребуется учетная запись хранения для отправки VHD-файла. На этом шаге можно выбрать существующую учетную запись или создать новую. Чтобы создать учетную запись хранения, см. раздел [Создание учетной записи хранения](../storage/storage-create-storage-account.md).

При загрузке файла .vhd его можно поместить в любом месте внутри хранилища больших двоичных объектов. В приведенных ниже примерах команд **BlobStorageURL** — это URL-адрес для учетной записи хранения,которую вы планируете использовать, а **YourImagesFolder** — это контейнер внутри хранилища больших двоичных объектов, где будут храниться образы. **VHDName** — это метка, которая отображается на [портале Azure](http://portal.azure.com) или [классическом портале Azure](http://manage.windowsazure.com) для идентификации виртуального жесткого диска. **PathToVHDFile** — это полный путь и имя файла .vhd на виртуальной машине.


### При использовании CLI Azure

Для отправки изображения выполните следующую команду в командной строке Azure:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

Дополнительные сведения см. в [справочнике по командам Azure CLI для управления службами Azure](../virtual-machines-command-line-tools.md).


### При использовании PowerShell

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Дополнительные сведения см. в разделе [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0323_2016-->
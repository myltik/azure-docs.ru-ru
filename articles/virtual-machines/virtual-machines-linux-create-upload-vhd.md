<properties
	pageTitle="Создание и передача виртуального жесткого диска с операционной системой Linux в Azure"
	description="Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/15/2015"
	ms.author="dkshir"/>

# Создание и передача виртуального жесткого диска с операционной системой Linux

В этой статье показано, как создать и передать виртуальный жесткий диск (VHD-файл), чтобы использовать его в качестве образа для создания виртуальных машин в Azure. Вы узнаете, как подготовить операционную систему, чтобы использовать ее в качестве образа для создания нескольких виртуальных машин.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Виртуальная машина в Azure работает под управлением операционной системы на основе образа, который вы выбрали при создании виртуальной машины. Образы хранятся в формате виртуальных жестких дисков (VHD-файлы) в учетной записи хранения. С более подробной информацией можно ознакомиться в разделе [О дисках и образах в Azure](https://msdn.microsoft.com/library/azure/jj672979.aspx).

При создании виртуальной машины можно настроить некоторые параметры операционной системы, чтобы оптимизировать их под приложение, которое вы хотите запустить. Инструкции см. в разделе [Создание настраиваемой виртуальной машины](virtual-machines-create-custom.md).

**Важно!** Соглашение об уровне обслуживания платформы Azure применяется к виртуальным машинам, работающим под управлением ОС Linux, только если один из рекомендуемых дистрибутивов используется вместе со сведениями о конфигурации, указанными в статье «Поддерживаемые версии» раздела [Linux с рекомендуемыми дистрибутивами Azure](virtual-machines-../linux-endorsed-distributions.md). Все дистрибутивы Linux в коллекции образов Azure — это рекомендуемые дистрибутивы, уже имеющие необходимую конфигурацию.


##Предварительные требования##
В данной статье предполагается, что у вас есть следующие элементы:

- **Сертификат управления**. Создан (и экспортирован в файл .cer) сертификат управления для подписки, для которой требуется загрузить VHD. Дополнительную информацию о создании сертификатов см. в разделе [Создание и отправка сертификата управления в Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).

- **Операционная система Linux установлена в файле VHD**. Вы установили поддерживаемую операционную систему Linux на виртуальный жесткий диск. Существует несколько средств для создания файлов VHD. Например, для создания файла VHD и установки операционной системы можно использовать решения для виртуализации, такие как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

	**Важно!** Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.

	Список рекомендованных дистрибутивов см. в документе [Linux в рекомендованных дистрибутивах Azure](../linux-endorsed-distributions.md). Кроме того, в разделе, приведенном в конце данной статьи, содержатся [сведения о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-vhd-generic.md).

- **Интерфейс командной строки Azure**. При использовании операционной системы Linux для создания образа следует использовать [интерфейс командной строки Azure](../virtual-machines-command-line-tools.md) для передачи VHD-файла.

- **Инструменты Azure Powershell**. Командлет `Add-AzureVhd` также можно использовать для передачи VHD-файла. Чтобы скачать командлеты Azure Powershell, перейдите на страницу [Загрузки Azure](http://azure.microsoft.com/downloads/). Справочные сведения см. в разделе [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

## <a id="prepimage"> </a>Шаг 1. Подготовка образа для передачи ##

Microsoft Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](../linux-endorsed-distributions.md)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure:

- **[Дистрибутивы на основе CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES и openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Прочее — нерекомендованные дистрибутивы](virtual-machines-linux-create-upload-vhd-generic.md)**

Дополнительные советы по подготовке образов Linux для Azure см. в разделе **[Общие замечания по установке Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)**.

После выполнения этапов, описанных в вышеуказанных руководствах, у вас должен быть VHD-файл для передачи в Azure.


## <a id="connect"> </a>Шаг 2. Подготовка подключения к Azure ##

Перед загрузкой файла VHD необходимо установить безопасное соединение между компьютером и вашей подпиской в Azure.


### При использовании CLI Azure

1. Откройте окно CLI Azure.

2. Введите:

	`azure login`

	При появлении запроса введите имя пользователя и пароль.

**Или**, вместо этого используйте файл PublishSettings:

1. Откройте окно CLI Azure.

2. Введите:

	`azure account download`

	Эта команда открывает окно браузера и автоматически загружает файл .publishsettings, содержащий информацию и сертификат для подписки Azure.

3. Сохраните файл .publishsettings.

4. Тип:

	`azure account import <PathToFile>`

	`<PathToFile>` — это полный путь к файлу PUBLISHSETTINGS.


### При использовании Azure PowerShell

1. Откройте окно Azure PowerShell.

2. Тип:

	`Get-AzurePublishSettingsFile`

	Эта команда открывает окно браузера и автоматически загружает файл .publishsettings, содержащий информацию и сертификат для подписки Azure.

3. Сохраните файл .publishsettings.

4. Тип:

	`Import-AzurePublishSettingsFile <PathToFile>`

	`<PathToFile>` — это полный путь к файлу PUBLISHSETTINGS.

	Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md)


## <a id="upload"> </a>Шаг 3. Передача образа в Azure ##

### При использовании CLI Azure

Используйте CLI Azure для передачи образа. Можно передать образ, используя следующую команду:

		azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### При использовании PowerShell

Потребуется учетная запись хранения для отправки VHD-файла. На этом шаге можно выбрать существующую учетную запись или создать новую. Чтобы создать учетную запись хранения, см. раздел [Создание учетной записи хранения](../storage-create-storage-account.md)

При загрузке файла .vhd его можно поместить в любом месте внутри хранилища больших двоичных объектов. В приведенных ниже примерах команд **BlobStorageURL** — это URL-адрес для учетной записи хранения,которую вы планируете использовать, а **YourImagesFolder** — это контейнер внутри хранилища больших двоичных объектов, где будут храниться образы. **VHDName** — это метка, которая отображается в портале управления для идентификации виртуального жесткого диска. **PathToVHDFile** — это полный путь и имя файла .vhd.

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Дополнительную информацию см. в разделе [Add-AzureVhd]((https://msdn.microsoft.com/library/azure/dn495173.aspx).




[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=July15_HO4-->
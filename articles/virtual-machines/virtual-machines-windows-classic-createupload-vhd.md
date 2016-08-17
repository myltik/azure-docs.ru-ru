<properties
	pageTitle="Создание и передача образа виртуальной машины с помощью Powershell | Microsoft Azure"
	description="Узнайте, как создать и передать универсальный образ Windows Server (VHD) с помощью классической модели развертывания и Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Создание и передача виртуального жесткого диска Windows Server в Azure

В этой статье показан процесс передачи собственного универсального образа виртуальной машины как виртуального жесткого диска (VHD-файла) и дальнейшего его использования для создания виртуальных машин. Дополнительные сведения о дисках и виртуальных жестких дисках в Microsoft Azure см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Вы также можете [записать](virtual-machines-windows-capture-image.md) и [отправить](virtual-machines-windows-upload-image.md) виртуальную машину, используя модель диспетчера ресурсов.

## Предварительные требования

В этой статье предполагается, что у вас есть следующие компоненты.

1. **Подписка Azure**. Если подписка отсутствует, можно [создать учетную запись Azure бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F).

2. **[Microsoft Azure PowerShell](../powershell-install-configure.md)**. У вас уже установлен и настроен на использование подписки модуль Microsoft Azure PowerShell.

3. **VHD-файл**. Поддерживаемая операционная система Windows, сохраненная в VHD-файле и подключенная к виртуальной машине.

> [AZURE.IMPORTANT] Формат VHDX не поддерживается в Microsoft Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Дополнительные сведения см. в [этом блоге](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Шаг 1. Подготовка виртуального жесткого диска 

Перед отправкой виртуального жесткого диска в Azure его необходимо подготовить с помощью средства Sysprep к использованию в качестве образа. Дополнительные сведения о программе Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

В виртуальной машине, в которую была установлена операционная система, сделайте следующее:

1. Войдите в операционную систему.

2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\\system32\\sysprep** и запустите файл `sysprep.exe`.

	![Откройте окно командной строки.](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	Откроется диалоговое окно **Программа подготовки системы**.

	![Запустите Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  В разделе **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.

5.  В разделе **Параметры завершения работы** выберите **Завершение работы**.

6.  Нажмите кнопку **ОК**.

## Шаг 2. Создание учетной записи хранения и контейнера

Вам потребуется учетная запись хранения Azure, в которую можно будет отправить VHD-файл. Здесь показано, как создать учетную запись или получить нужные сведения из существующей учетной записи. Замените переменные в &lsaquo; скобках &rsaquo; собственными данными.

1. Вход

		Add-AzureAccount

1. Настройте свою подписку Azure.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным. Его длина должна составлять от 3 до 24 знаков. Имя может представлять собой любое сочетание букв и цифр. Необходимо также указать расположение, например "Восток США".
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Используйте новую учетную запись хранения по умолчанию.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Создайте новый контейнер.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## Шаг 3. Передача VHD-файла

Для передачи VHD-файла используйте командлет [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите следующую команду и замените переменные в &lsaquo; скобках &rsaquo; собственными данными.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## Шаг 4. Добавление образа в список пользовательских образов

Чтобы добавить образ в список пользовательских образов, используйте командлет [Add-AzureVMImage])(https://msdn.microsoft.com/library/mt589167.aspx).

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## Дальнейшие действия

Теперь вы можете [создать пользовательскую виртуальную машину](virtual-machines-windows-classic-createportal.md) с помощью переданного образа.

<!---HONumber=AcomDC_0803_2016-->
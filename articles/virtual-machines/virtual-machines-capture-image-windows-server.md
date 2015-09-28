<properties
	pageTitle="Запись образа виртуальной машины под управлением Windows Server"
	description="Узнайте, как записать образ виртуальной машины (VM) Azure под управлением Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="cynthn"/>

#Как записать виртуальную машину Windows, чтобы использовать в качестве образа#

В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве образа при создании других виртуальных машин. Этот образ включает диск с операционной системой и все диски с данными, подключенные к виртуальной машине. Он не включает сетевые конфигурации, поэтому необходимо настроить их при создании других виртуальных машин на базе этого шаблона.

Azure хранит образ в папке **Мои образы**. Здесь же хранятся все отправленные вами образы. Подробнее об образах см. в статье [Образы виртуальных машин](virtual-machines-images.md).

##Перед началом работы##

В этой статье предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:

- [Создание настраиваемой виртуальной машины под управлением Windows][]
- [Подключение диска с данными к виртуальной машине][]

> [AZURE.WARNING]Этот процесс удаляет исходную виртуальную машину после ее записи и не предназначен для создания резервной копии виртуальной машины. Единственный способ сделать это — использовать службу архивации Azure, предварительная версия которой доступна в определенных регионах. Подробнее см. в статье [Резервное копирование виртуальных машин Azure](../backup/backup-azure-vms.md). Доступны другие решения от сертифицированных партнеров. Чтобы узнать, что сейчас доступно, выполните поиск в Azure Marketplace.

##Запись виртуальной машины##

1. На [портале Azure](http://manage.windowsazure.com) **подключитесь**к виртуальной машине. Инструкции см. в разделе [Как войти в виртуальную машину под управлением Windows Server][].

2.	Откройте окно командной строки с правами администратора.

3.	Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.

4. 	Откроется диалоговое окно **Программа подготовки системы**. Выполните следующее:

	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение][].

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Запуск Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep завершит работу виртуальной машины, при этом ее состояние на портале Azure изменится на **Остановлена**.

8.	На портале Azure щелкните **Виртуальные машины**, а затем выберите виртуальную машину, которую требуется записать.

9.	На панели команд щелкните **Запись**.

	![Запись виртуальной машины](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Отображается диалоговое окно **Запись виртуальной машины**.

10.	В поле **Имя образа** введите имя нового образа.

11.	Прежде чем добавить образ Windows Server в набор настраиваемых образов, его необходимо обобщить, запустив программу Sysprep, как описано в предыдущих шагах. Нажмите кнопку **Я запустил Sysprep на виртуальной машине**, чтобы указать, что это выполнено.

12.	Щелкните галочку, чтобы записать образ. Новый образ станет доступным в разделе **Образы**.

 	![Успешная запись образа](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Дальнейшие действия##
Образ готов к использованию для создания виртуальных машин. Для этого с помощью пункта меню **Из коллекции** создайте виртуальную машину и выберите только что созданный образ. Необходимые инструкции см. в разделе [Создание настраиваемой виртуальной машины, работающей под управлением ОС Windows][].

[Создание настраиваемой виртуальной машины под управлением Windows]: virtual-machines-windows-create-custom.md
[Создание настраиваемой виртуальной машины, работающей под управлением ОС Windows]: virtual-machines-windows-create-custom.md
[Подключение диска с данными к виртуальной машине]: storage-windows-attach-disk.md
[Как войти в виртуальную машину под управлением Windows Server]: virtual-machines-log-on-windows-server.md
[Как использовать Sysprep: введение]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=Sept15_HO3-->
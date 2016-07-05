<properties
	pageTitle="Запись образа виртуальной машины Azure Windows | Microsoft Azure"
	description="Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания."
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
	ms.date="06/16/2016"
	ms.author="cynthn"/>

#Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-capture-image.md).


В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве образа при создании других виртуальных машин. Этот образ включает диск с операционной системой и все диски с данными, подключенные к виртуальной машине. Он не включает сетевые конфигурации, поэтому необходимо настроить их при создании других виртуальных машин на базе этого образа.

Azure хранит образ в папке **Мои образы**. Здесь же хранятся все отправленные вами образы. Подробнее об образах см. в статье [Образы виртуальных машин](virtual-machines-linux-classic-about-images.md).

##Перед началом работы##

В этой статье предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:

- [Создание виртуальной машины из образа](virtual-machines-windows-classic-createportal.md)
- [Подключение диска с данными к виртуальной машине](virtual-machines-windows-classic-attach-disk.md)

> [AZURE.WARNING] Этот процесс удаляет исходную виртуальную машину, как только она будет зарегистрирована.

Перед записью образа виртуальной машины Azure рекомендуется создать резервную копию целевой виртуальной машины. Это можно сделать с помощью службы архивации Azure. Дополнительные сведения см. в статье [Резервное копирование виртуальных машин Azure](../backup/backup-azure-vms.md). Доступны другие решения от сертифицированных партнеров. Чтобы узнать, что сейчас доступно, выполните поиск в Azure Marketplace.


##Запись виртуальной машины

1. На [классическом портале Azure](http://manage.windowsazure.com) **подключитесь**к виртуальной машине. Указания см. в разделе [Вход в виртуальную машину под управлением Windows Server][].

2.	Откройте окно командной строки с правами администратора.

3.	Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.

4. 	Откроется диалоговое окно **Программа подготовки системы**. Выполните следующее:

	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение][].

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Запуск Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.	Sysprep завершит работу виртуальной машины, при этом ее состояние на классическом портале Azure изменится на **Остановлена**.

8.	На классическом портале Azure щелкните **Виртуальные машины**, а затем выберите виртуальную машину, которую требуется записать.

9.	На панели команд щелкните **Запись**.

	![Запись виртуальной машины](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

	Отображается диалоговое окно **Запись виртуальной машины**.

10.	В поле **Имя образа** введите имя нового образа.

11.	Прежде чем добавить образ Windows Server в набор настраиваемых образов, его необходимо обобщить, запустив программу Sysprep, как описано в предыдущих шагах. Щелкните **Я запустил Sysprep на виртуальной машине**, чтобы указать, что это выполнено.

12.	Щелкните галочку, чтобы записать образ. Новый образ станет доступным в разделе **Образы**.

 	![Успешная запись образа](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##Дальнейшие действия

Образ готов к использованию для создания виртуальных машин. Для этого с помощью пункта меню **Из коллекции** создайте виртуальную машину и выберите только что созданный образ. Инструкции см. в статье [Создание виртуальной машины из образа](virtual-machines-windows-classic-createportal.md).



[Вход в виртуальную машину под управлением Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Как использовать Sysprep: введение]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=AcomDC_0622_2016-->
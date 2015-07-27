<properties
	pageTitle="Запись образа виртуальной машины под управлением Windows Server"
	description="Узнайте, как записать образ виртуальной машины (ВМ) Azure под управлением Windows Server 2008 R2."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#Создание образа виртуальной машины Windows для его последующего использования в качестве шаблона#

В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве шаблона при создании других виртуальных машин. Этот шаблон включает диск с операционной системой и все диски с данными, подключенные к виртуальной машине. Он не включает сетевые конфигурации, поэтому необходимо настроить их при создании других виртуальных машин на базе этого шаблона.

Azure воспринимает этот шаблон как образ и хранит его в папке **Мои образы**. В этой папке также хранятся все переданные вами образы. Дополнительные сведения об образах см. в статье [Сведения об образах виртуальных машин в Azure][].

##Перед началом работы##

В шагах этой статьи предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:

- [Создание настраиваемой виртуальной машины][]
- [Подключение диска с данными к виртуальной машине][]

##Создание образа виртуальной машины##

1. Подключитесь к виртуальной машине, щелкнув **Подключить** на панели команд. Дополнительные сведения см. в статье [Как войти в виртуальную машину под управлением Windows Server][].

2.	Откройте окно командной строки с правами администратора.


3.	Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.


4. 	Откроется диалоговое окно **Программа подготовки системы**. Выполните следующее:


	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение][].

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Запуск Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep завершит работу виртуальной машины, при этом ее состояние на [портале Azure](http://manage.windowsazure.com) изменится на **Остановлена**.


8.	Щелкните **Виртуальные машины**, а затем выберите виртуальную машину, которую требуется записать.

9.	На панели команд щелкните **Запись**.

	![Запись виртуальной машины](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Отображается диалоговое окно **Запись виртуальной машины**.

10.	В поле **Имя образа** введите имя нового образа.

11.	Прежде чем добавить образ Windows Server в набор настраиваемых образов, его необходимо обобщить, запустив программу Sysprep, как описано в предыдущих шагах. Нажмите кнопку **Я запустил Sysprep на виртуальной машине**, чтобы указать, что это выполнено.

12.	Щелкните галочку, чтобы записать образ.

  **ПРИМЕЧАНИЕ. При записи образа универсальной виртуальной машины эта виртуальная машина будет удалена.**

 Теперь новый образ доступен в разделе **Образы**. ![Успешная запись образа](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Дальнейшие действия##
Образ готов к использованию в качестве шаблона для создания виртуальных машин. Для этого с помощью пункта меню **Из коллекции** создайте настраиваемую виртуальную машину и выберите только что созданный образ. Необходимые инструкции см. в статье [Создание настраиваемой виртуальной машины][].


[Сведения об образах виртуальных машин в Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Создание настраиваемой виртуальной машины]: virtual-machines-create-custom.md
[Подключение диска с данными к виртуальной машине]: storage-windows-attach-disk.md
[Как войти в виртуальную машину под управлением Windows Server]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[Как использовать Sysprep: введение]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=July15_HO3-->
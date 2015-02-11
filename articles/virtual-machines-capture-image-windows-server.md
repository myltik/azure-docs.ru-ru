<properties urlDisplayName="Capture an image" pageTitle="Запись образа виртуальной машины под управлением Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Узнайте, как записать образ виртуальной машины (ВМ) Azure под управлением Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

# Как записать виртуальную машину Windows, чтобы использовать в качестве шаблона

В этой статье показано, как записать виртуальную машину Azure под управлением Windows, чтобы использовать ее в качестве шаблона при создании других виртуальных машин. Данный шаблон виртуальной машины включает в себя диск операционной системы и прочие диски данных, присоединенные к виртуальной машине. Он не включает в себя сетевую конфигурацию, поэтому ее необходимо настроить при создании других виртуальных машин, использующих шаблон.

Microsoft Azure воспринимает этот шаблон как образ и хранит его в папке **Мои образы**. В этой папке также хранятся все переданные вами образы. Дополнительную информацию об образах см. в разделе [Образы виртуальных машин в Azure] [].

## Перед началом работы

В шагах этой статьи предполагается, что вы уже создали виртуальную машину Azure и настроили операционную систему, а также присоединили диски данных. Если это еще не сделано, см. данные указания:

- [Создание настраиваемой виртуальной машины] []
- [Как присоединить диск данных к виртуальной машине] []

## Запись виртуальной машины

1. Подключитесь к виртуальной машине, щелкнув **Подключить** на панели команд. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Windows Server] [].

2.	Откройте окно командной строки с правами администратора.


3.	Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.


4. 	Откроется диалоговое окно **Программа подготовки системы**. Выполните следующее:


	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [How to Use Sysprep: An Introduction (Как использовать Sysprep: введение)][] .

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Программа Sysprep завершит работу виртуальной машины, в результате чего ее состояние на [портале управления](http://manage.windowsazure.com) будет изменен на **Остановлена**.


8.	Нажмите кнопку **Виртуальные машины**, а затем выберите виртуальную машину, которую требуется записать.

9.	На панели команд щелкните **Запись**.

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Отобразится диалоговое окно **Запись виртуальной машины**.

10.	В поле **Имя образа** введите имя нового образа.

11.	Прежде чем добавить образ Windows Server в набор настраиваемых образов, его необходимо обобщить, запустив программу Sysprep, как описано в предыдущих шагах. Щелкните **Я запустил Sysprep на виртуальной машине**, чтобы указать, что это выполнено.

12.	Щелкните галочку, чтобы записать образ. При записи образа обобщенной виртуальной машины эта виртуальная машина будет удалена.

	Новый образ станет доступным в разделе **Образы**.

	![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

## Дальнейшие действия
Образ готов к использованию в качестве шаблона для создания виртуальных машин. Для этого создайте настраиваемую виртуальную машину **Из коллекции** и выберите образ, который вы только что создали. Указания см. в разделе [Создание настраиваемой виртуальной машины] [].

	
[Образы виртуальных машин в Azure]: http://msdn.microsoft.com/ru-ru/library/azure/dn790290.aspx
[Создание настраиваемой виртуальной машины]: ../virtual-machines-create-custom/
[Как присоединить диск данных к виртуальной машине]: ../storage-windows-attach-disk/
[Как войти в виртуальную машину под управлением Windows Server]:http://www.windowsazure.com/ru-ru/manage/windows/how-to-guides/log-on-a-windows-vm/
[How to Use Sysprep: An Introduction (Как использовать Sysprep: введение)]: http://technet.microsoft.com/ru-ru/library/bb457073.aspx
[Запуск Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Ввод параметров Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[Виртуальная машина остановлена]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Запись образа виртуальной машины]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Ввод имени образа]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Успешная запись образа]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Использование записанного образа]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=35.1-->

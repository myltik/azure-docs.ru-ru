<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Создание виртуальной машины под управлением Windows Server" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Создание виртуальной машины под управлением Windows Server #

Из данного руководства вы узнаете, насколько просто создавать виртуальные машины Azure под управлением Windows Server с помощью коллекции образов на портале управления Microsoft Azure. Коллекция образов содержит широкое разнообразие образов, в том числе операционные системы Windows, операционные системы на основе Linux, а также образы приложений. 

> [WACOM.NOTE] Чтобы пользоваться этим руководством, не нужен опыт работы с виртуальными машинами. Однако необходимо иметь учетную запись Azure. Вы можете создать бесплатную пробную учетную запись всего за несколько минут. Подробную информацию см. в разделе [Создание учетной записи Azure](http://www.windowsazure.com/ru-ru/develop/php/tutorials/create-a-windows-azure-account/). 

В этом учебнике показано:

- [Создание виртуальной машины](#createvirtualmachine)
- [Как войти в систему на виртуальной машине после ее создания](#logon)
- [Как присоединить диск данных к новой виртуальной машине](#attachdisk)

Более подробную информацию можно найти в разделе [Виртуальные машины](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Создание виртуальной машины##

В этом разделе показано, как использовать метод **Из коллекции** на портале управления для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание**. Например, чтобы присоединить виртуальную машину к виртуальной сети, следует использовать вариант **Из коллекции**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Как войти в систему на виртуальной машине после ее создания ##

В настоящем разделе показано, как войти в виртуальную машину для управления ее настройками и приложениями, которые будут на ней выполняться.

1. Войдите на [портал управления](http://manage.windowsazure.com) Azure.

2. Нажмите кнопку **Виртуальные машины**, а затем выберите виртуальную машину **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. На панели команд щелкните **Подключить**.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Щелкните **Открыть**, чтобы использовать файл протокола удаленного рабочего стола, автоматически созданного для виртуальной машины.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Щелкните **Подключить**.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. В поле "Пароль" введите имя пользователя и пароль, заданные при создании виртуальной машины, и нажмите **ОК**.

7. Щелкните **Да** для проверки удостоверения виртуальной машины.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Теперь вы сможете работать с виртуальной машиной, так же как и с любым сервером в своем офисе.

## <a id="attachdisk"> </a>Как присоединить диск данных к новой виртуальной машине ##

В настоящем разделе показано, как присоединить пустой диск данных к виртуальной машине. Дополнительные сведения о присоединении пустых и существующих дисков можно найти в [руководстве по присоединению диска данных] (http://www.windowsazure.com/ru-ru/documentation/articles/storage-windows-attach-disk/).

1. Войдите на [портал управления](http://manage.windowsazure.com) Azure.

2. Нажмите кнопку **Виртуальные машины**, а затем выберите виртуальную машину **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Сначала вы можете попасть на страницу быстрого запуска. В этом случае выберите **Панель мониторинга** в верхней части страницы.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. На панели команд нажмите кнопку **Присоединить**, а затем - **Присоединить пустой диск**, после того как появится всплывающее окно.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. Значения **Имя виртуальной машины**, **Расположение хранилища**, **Имя файла** и **Настройки кэша узла** уже определены. Достаточно ввести размер диска. Введите **5** в поле **Размер**. Установите флажок, чтобы присоединить пустой диск данных к виртуальной машине.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] Все диски создаются из VHD-файла в хранилище Windows Azure. В поле **Имя файла** можно ввести имя для добавляемого в хранилище VHD-файла, однако Azure создаст имя диска автоматически.

6. Перейдите обратно на панель мониторинга, чтобы убедиться в успешном присоединении пустого диска данных к виртуальной машине. Он будет указан как второй диск в списке **Диски** вместе с диском операционной системы.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	После присоединения к виртуальной машине диск данных отключен и не инициализирован. Чтобы использовать его для хранения данных, необходимо предварительно войти в систему на виртуальной машине и инициализировать этот диск.

7. Подключитесь к виртуальной машине, выполнив действия, описанные в предыдущем разделе [Как войти в систему на виртуальной машине после ее создания] (#logon).

8. После входа в систему на виртуальной машине откройте **Диспетчер сервера**. В левой панели выберите **Файловые службы и службы хранилища**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. В развернутом меню выберите **Диски**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. В разделе **Диски** указаны три диска в списке: диск 0, диск 1 и диск 2. Диск 0 - это диск операционной системы, диск 1 - временный диск ресурсов (который не должен использоваться как хранилище данных), а диск 2 - диск данных, который вы подсоединили к виртуальной машине. Обратите внимание, что диск данных имеет емкость 5 ГБ, как указывалось ранее. Щелкните диск 2 правой кнопкой мыши и выберите **Инициализировать**.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Нажмите кнопку **Да**, чтобы начать инициализацию.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Щелкните диск 2 правой кнопкой мыши еще раз и выберите **Новый том**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Завершите работу мастера, используя значения по умолчанию. После завершения работы мастера новый том появится в списке раздела **Тома**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Диск подключен и готов к использованию с новой буквой диска. 
	
##Дальнейшие действия 

Для получения дополнительных сведений о настройке виртуальных машин Windows в Azure см. следующие статьи:

[Подключение к виртуальным машинам в облачной службе](http://www.windowsazure.com/ru-ru/documentation/articles/cloud-services-connect-virtual-machine/)

[Создание и отправка собственного виртуального жесткого диска, содержащего операционную систему Windows](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Присоединение диска данных к виртуальной машине](http://www.windowsazure.com/ru-ru/documentation/articles/storage-windows-attach-disk/)

[Управление доступностью виртуальных машин](http://www.windowsazure.com/ru-ru/documentation/articles/manage-availability-virtual-machines/)

[Виртуальные машины в Azure]: #virtualmachine
[Создание виртуальной машины]: #custommachine
[Как войти в систему на виртуальной машине после ее создания]: #logon
[Как присоединить диск данных к новой виртуальной машине]: #attachdisk
[Настройка связи с виртуальной машиной]: #endpoints



<properties 
	pageTitle="Создание виртуальной машины под управлением Windows на портале предварительной версии Azure" 
	description="Узнайте, как создать виртуальную машину Azure под управлением Windows, используя Marketplace на портале предварительной версии Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="danlep,kathydav,rasquill"/>

<!---Рабочий процесс может вызывать путаницу... Придется выбрать нужный размер с помощью **просмотра всех ценовых уровней**. Затем щелкните Выбрать на этой панели и область **Рекомендованные ценовые уровни** для применения. Но даже после этого, похоже, что не удалось выбрать учетную запись хранения Premium, показанную как "стандартная GRS", когда я проверял, какой тип учетной записи хранения использовать для виртуальной машины. 
-->

<!-- Preview portal screenshots getting a little out of date. Please refresh at next update.
-->

# Создание виртуальной машины на портале предварительной версии Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Портал Azure</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Портал предварительной версии Azure</a></div>

В этом учебнике показано, насколько просто создавать виртуальные машины Azure (ВМ) под управлением Windows, используя в качестве примера образ Windows Server из Azure Marketplace на портале предварительной версии Azure. Marketplace содержит большое разнообразие образов, в том числе операционные системы Windows, операционные системы на основе Linux, а также образы приложений. 

> [AZURE.NOTE] Чтобы пользоваться этим руководством, не нужен опыт работы с виртуальными машинами. Однако необходимо иметь учетную запись Azure. Вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Создание учетной записи Azure](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/). 

В этом учебнике показано:

- [Создание виртуальной машины](#createvirtualmachine)
- [Как войти в виртуальную машину после ее создания](#logon)

Более подробную информацию см. в разделе [Виртуальные машины](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Создание виртуальной машины##

В этом разделе показано, как с помощью портала предварительной версии создать виртуальную машину. В качестве примера используется центр обработки данных Windows Server 2012 R2. Для большей части параметров конфигурации можно использовать значения по умолчанию Azure и создать ВМ буквально за несколько минут.

> [AZURE.NOTE] Доступность образов зависит от вашей подписки. В этом учебнике используется образ Windows Server, но при подписке на MSDN могут быть доступны дополнительные образы, включая образы рабочего стола. 
 

1. Войдите на [портал предварительной версии Azure](https://portal.azure.com). Воспользуйтесь [Бесплатной пробной версией](http://www.windowsazure.com/pricing/free-trial/), если у вас пока нет подписки.

2. В меню концентратора нажмите кнопку **Создать**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. В разделе **Создание** последовательно щелкните **Все**, **Виртуальные машины**, **Центр обработки данных Windows Server 2012 R2** и нажмите кнопку **Создать**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. В разделе **Создание виртуальной машины** введите нужное **имя узла** для виртуальной машины, административное **имя пользователя** и надежный **пароль** в соответствующих полях.  

	>[AZURE.NOTE] **Имя пользователя** относится к административной учетной записи, используемой для управления сервером. Создайте уникальный пароль для этой учетной записи и запомните его. **Имя пользователя и пароль понадобятся для входа на виртуальную машину**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Просмотрите параметры по умолчанию, такие как **Уровень ценообразования**, **Дополнительная конфигурация** и **Расположение**. Значения, выбранные для этих параметров, влияют на размер виртуальной машины, а также сетевые возможности, такие как членство в домене. Например, чтобы опробовать хранилище Premium на виртуальной машине, понадобится выбрать регион и размер, поддерживающие его. 

	>[AZURE.NOTE] Хранилище Premium доступно в режиме предварительной версии для виртуальных машин серии DS в определенных регионах. Дополнительные сведения см. в разделе [Хранилище Premium: хранилище высокой производительности для рабочих нагрузок виртуальных машин Azure](http://azure.microsoft.com/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Закончив просмотр или обновление параметров, нажмите кнопку **Создать**.	

7. Пока Azure создает ВМ, можно отслеживать ход выполнения в разделе **Уведомления** в меню концентратора. После того как Azure создаст ВМ, она появится в начальной панели.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Как войти в виртуальную машину после ее создания ##

В настоящем разделе показано, как войти в ВМ для управления ее настройками и приложениями, которые будут на ней выполняться.

>[AZURE.NOTE] Информацию о требованиях и устранении неполадок см. в разделе [Подключение к виртуальной машине Azure по RDP или SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Войдите на [Портал предварительной версии Azure](https://portal.azure.com), если вы этого еще не сделали.

2. Щелкните ВМ на начальной панели. Если нужно ее найти, нажмите кнопку **Обзор**, а затем щелкните **Виртуальные машины**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. Затем выберите из списка свою виртуальную машину. 

	![Select your VM name from the list](./media/virtual-machines-windows-tutorial-azure-preview/vm_select_preview_portal.png)

4. В верхней части раздела виртуальной машины щелкните **Подключение**.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

5. Нажмите кнопку **Открыть**, чтобы использовать файл протокола удаленного рабочего стола, автоматически созданного для виртуальной машины.
	
6. Чтобы продолжить процесс подключения, нажмите кнопку **Подключиться**.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

7. Введите имя пользователя и пароль для учетной записи администратора виртуальной машины, а затем нажмите **ОК**.
	
8. Щелкните **Да** для проверки удостоверения виртуальной машины.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Теперь вы сможете работать с виртуальной машиной, так же как и с любым сервером.

##Дальнейшие действия 

Для получения дополнительных сведений о настройке виртуальных машин Windows в Azure см. следующие статьи:

[Подключение виртуальных машин в облачной службе](http://www.windowsazure.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Как создать и передать собственный виртуальный жесткий диск, содержащий операционную систему Windows](http://www.windowsazure.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Подключение дисков данных к виртуальной машине](http://www.windowsazure.com/documentation/articles/storage-windows-attach-disk/)

[Управление доступностью виртуальных машин](http://www.windowsazure.com/documentation/articles/manage-availability-virtual-machines/)

[О параметрах конфигурации виртуальной машины Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Создание виртуальной машины]: #custommachine
[Как войти в виртуальную машину после ее создания]: #logon

<!--HONumber=47-->

<properties urlDisplayName="Upload a VHD" pageTitle="Создание и передача виртуального жесткого диска Windows Server в Azure" metaKeywords="Azure VHD, передача виртуального жесткого диска" description="Узнайте, как создать и передать виртуальный жесткий диск с операционной системой Windows Server в Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />


#Создание и отправка виртуального жесткого диска Windows Server в Azure#

В этой статье показано, как передать виртуальный жесткий диск с операционной системой, чтобы использовать его в качестве образа для создания виртуальных машин. Дополнительные сведения об образах и дисках в Microsoft Azure см. в статье [Диски и образы в Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj672979.aspx).

**Примечание**. При создании виртуальной машины можно настроить параметры операционной системы, чтобы оптимизировать выполняемое приложение. Заданная конфигурация хранится на диске для данной виртуальной машины. Инструкции приведены в статье [Как создать настраиваемую виртуальную машину]](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-machines-windows-tutorial/).

##Необходимые компоненты
В данной статье предполагается, что у вас есть следующие элементы:

**Подписка на Azure**. Если у вас ее нет, вы можете создать бесплатную пробную учетную запись всего за несколько минут. Сведения об этом см. в статье [Создание учетной записи Azure](http://www.windowsazure.com/ru-ru/develop/php/tutorials/create-a-windows-azure-account/).  

**Microsoft Azure PowerShell**. У вас уже установлен модуль Microsoft Azure PowerShell. Чтобы скачать этот модуль, см. статью [Ресурсы Microsoft Azure для загрузки](http://www.windowsazure.com/ru-ru/downloads/). Учебник по установке и настройке PowerShell для подписки на Azure вы найдете [здесь](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/).

- Командлет [Add-AzureVHD](http://msdn.microsoft.com/ru-ru/library/windowsazure/dn205185.aspx), входящий в модуль Microsoft Azure PowerShell. Воспользуйтесь этим командлетом для передачи виртуального жесткого диска (VHD).

**Поддерживаемая операционная система Windows в VHD-файле**. Вы установили поддерживаемую операционную систему Windows Server на виртуальный жесткий диск. Существует несколько средств для создания VHD-файлов. Для создания файла VHD и установки операционной системы можно использовать решения для виртуализации, например Hyper-V. Инструкции об этом см. в статье [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/ru-ru/library/hh846766.aspx).

**Внимание**! Формат VHDX не поддерживается в Microsoft Azure. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или [командлета Convert-VHD](http://technet.microsoft.com/ru-ru/library/hh848454.aspx). Учебник об этом вы найдете [здесь](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
**Носитель с операционной системой Window Server**. Для этого задания требуется ISO-файл, содержащий операционную систему Windows Server. Поддерживаются следующие версии Windows Server:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>ОС</TH>
    <TH>SKU</TH>
    <TH>Пакет обновления</TH>
    <TH>Архитектура</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012 R2</TD>
    <TD>Все выпуски</TD>
    <TD>Недоступно</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>Все выпуски</TD>
    <TD>Недоступно</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>Все выпуски</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>


Эта задача включает следующие шаги:

- [Шаг 1. Подготовка образа для передачи] []
- [Шаг 2. Создание учетной записи хранения в Azure] []
- [Шаг 3. Подготовка подключения к Azure] []
- [Шаг 4. Передача VHD-файла] []

## <a id="prepimage"> </a>Шаг 1. Подготовка образа для передачи ##


Перед передачей образа в Azure его необходимо обобщить с помощью команды Sysprep. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение](http://technet.microsoft.com/ru-ru/library/bb457073.aspx).

В только что созданной виртуальной машине выполните следующие действия:

1. Войдите в операционную систему.

2. Откройте окно командной строки с правами администратора. Измените каталог, указав **%windir%\system32\sysprep**, а затем запустите файл sysprep.exe.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Откроется диалоговое окно **Программа подготовки системы**.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  В окне **Программа подготовки системы** выберите элемент ** 	Переход в окно приветствия системы (OOBE)** и установите флажок "Подготовка к использованию".

5.  В разделе **Параметры завершения работы** выберите **Завершение работы**.

6.  Нажмите кнопку **ОК**. 




## <a id="createstorage"> </a>Шаг 2. Создание учетной записи хранения в Azure ##

Учетная запись хранения представляет собой высший уровень пространства имен для доступа к службам хранения; эта запись связана с вашей подпиской Azure. Для загрузки в Azure файла VHD, который можно использовать для создания виртуальной машины, потребуется учетная запись хранения в Azure. Для создания учетной записи хранения можно использовать портал управления Azure.

1. Войдите на портал управления Azure.

2. На панели команд нажмите **Создать**.

3. Нажмите **Учетная запись хранения**, а затем **Быстрое создание**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Заполните поля, как показано ниже:

	
	
- В поле **URL-адрес** введите имя поддомена, которое нужно использовать в URL-адресе для учетной записи хранения. Записи могут содержать от 3 до 24 строчных букв и цифр. Это имя становится именем узла в URL, который используется для адресации ресурсов BLOB-объекта, очереди и таблицы для подписки.
			
- Выберите **территориальную группу** для учетной записи хранения. Указав территориальную группу, можно выполнить совместное размещение облачных служб в одном центре обработки данных с хранилищем.
		 
- Укажите, следует ли использовать **георепликацию** для учетной записи хранения. По умолчанию георепликация включена. Этот параметр позволяет бесплатно выполнять репликацию данных в дополнительное местоположение; таким образом в случае аварийного отказа, который не может быть устранен в основном местоположении, хранилище переключится на дополнительное местоположение. Дополнительное местоположение назначается автоматически и не может быть изменено. Если в соответствии с законодательными требованиями или организационной политикой требуется более жесткий контроль местоположения облачного хранилища, то георепликацию можно отключить. Однако следует помнить, что если вы опять включите георепликацию, с вас будет взята однократная плата за репликацию существующих данных в дополнительное местоположение. Службы хранения без георепликации предлагаются со скидкой. Дополнительную информацию об управлении георепликацией учетных записей хранения см. здесь: [Создание или удаление учетной записи хранения, а также управление нею](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Щелкните **Создать учетную запись хранения**.

	Учетная запись появится в списке **учетных записей хранения**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Теперь создайте контейнер для переданных виртуальных жестких дисков. Выберите элемент **Имя учетной записи хранения**, а затем - **Контейнеры**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Выберите элемент **Создать контейнер**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Укажите **имя** контейнера и выберите элемент **Политика доступа**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [WACOM.NOTE] По умолчанию доступ к контейнеру предоставляется только владельцу учетной записи. Чтобы разрешить общий доступ на чтение для больших двоичных объектов в контейнере, но не к свойствам и метаданным контейнера, используйте параметр "Общедоступный BLOB-объект". Чтобы разрешить полный общий доступ на чтение для контейнера и больших двоичных объектов, используйте параметр "Общедоступный контейнер".

## <a id="PrepAzure"> </a>Шаг 3. Подготовка подключения к Azure ##

Перед передачей VHD-файла необходимо установить безопасное соединение между компьютером и вашей подпиской в Microsoft Azure. Это можно сделать, используя метод Microsoft Azure Active Directory или метод сертификатов.

<h3>Использование Microsoft Azure AD</h3>

1. Откройте консоль Microsoft Azure PowerShell, как описано в разделе [Практическое руководство. Установка Microsoft Azure PowerShell](#Install).

2. Введите следующую команду:  
	`Add-AzureAccount`
	
	После выполнения команды откроется окно входа, и вы сможете войти, используя свою рабочую или школьную учетную запись.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Microsoft Azure выполняет аутентификацию и сохраняет учетные данные, а затем закрывает окно.

<h3>Использование сертификата</h3> 

1. Откройте окно Microsoft Azure PowerShell. 

2.	Введите: 
	`Get-AzurePublishSettingsFile`.


3. В открывшемся окне браузера появится запрос на загрузку PUBLISHSETTINGS-файла. Он содержит информацию и сертификат для подписки Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Сохраните PUBLISHSETTINGS-файл. 

4. Введите: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	<PathToFile> - полный путь к PUBLISHSETTINGS-файлу. 


	Дополнительные сведения см. в статье [Начало работы с командлетами Microsoft Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554332.aspx) 
	
	Дополнительные сведения об установке и настройке PowerShell см. в статье [Как установить и настроить Microsoft Azure PowerShell](http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/) 


## <a id="upload"> </a>Шаг 4. Передача VHD-файла ##

При загрузке VHD-файла его можно поместить в любом месте внутри хранилища BLOB-объектов. В приведенных ниже примерах команд **BlobStorageURL** - это URL-адрес для учетной записи хранения, созданный при выполнении шага 2, **YourImagesFolder** - это контейнер внутри хранилища BLOB-объектов, где будут храниться образы. **VHDName** - метка, отображающаяся на портале управления для идентификации виртуального жесткого диска. **PathToVHDFile** - имя VHD-файла и полный путь к нему. 


1. В окне Microsoft Azure PowerShell, использованном при выполнении предыдущего шага, введите:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Дополнительные сведения о командлете Add-AzureVhd см. в статье [Add-AzureVhd](http://msdn.microsoft.com/ru-ru/library/dn495173.aspx).

##Добавление образа в список настраиваемых образов ##
После загрузки файла VHD его можно добавить в качестве образа в список пользовательских образов, связанных с подпиской.

1. На портале управления в разделе **Все элементы** выберите элемент **Виртуальные машины**.

2. В разделе "Виртуальные машины" выберите элемент **Образы**.

3. А затем выберите элемент **Создать образ**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. В окне **Создание образа на основе VHD** выполните следующее:
 	
	- Укажите **имя**.
	- Укажите **описание**.
	- Чтобы указать **URL-адрес VHD-файла**, нажмите кнопку папки для открытия диалогового окна, приведенного ниже
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)
	- Укажите учетную запись хранения с VHD-файлом, а затем выберите элемент **Открыть**. После этого опять откроется окно **Создание образа на основе VHD**.
	- Вернувшись к нему****, выберите элемент "Семейство операционных систем".
	- Отметьте пункт **Я выполнил Sysprep на виртуальной машине, связанной с этим VHD**, чтобы подтвердить обобщение операционной системы при выполнении шага 1, а затем нажмите **ОК**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **НЕОБЯЗАТЕЛЬНО.** Вы также можете использовать командлет Add-AzureVMImage из модуля Azure PowerShell, чтобы добавить VHD-файл в качестве образа.

	В окне Microsoft Azure PowerShell введите следующее:

	`Add-AzureVMImage -ImageName <имя_образа> -MediaLocation <расположение_VHD> -OS <тип_операционной_системы_в_VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. После выполнения указанных выше шагов отобразится новый образ, когда вы откроете вкладку **Образы**. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Теперь при создании виртуальной машины вы сможете воспользоваться этим новым образом. Чтобы отобразить его, выберите элемент **Мои образы**. Инструкции об этом см. в статье [Создание виртуальной машины под управлением Windows Server](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

## Дальнейшие шаги ##
 

После создания виртуальной машины попробуйте создать виртуальную машину SQL Server. Инструкции см. в статье [Подготовка виртуальной машины SQL Server в Azure](http://www.windowsazure.com/ru-ru/documentation/articles/virtual-machines-provision-sql-server/). 

[Шаг 1. Подготовка образа для передачи]: #prepimage
[Шаг 2. Создание учетной записи хранения в Azure]: #createstorage
[Шаг 3. Подготовка подключения к Azure]: #prepAzure
[Шаг 4. Передача VHD-файла]: #upload

<!--HONumber=35.2-->

<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Лес Active Directory" pageTitle="Установка леса Active Directory в сети Windows Azure" metaKeywords="" description="Учебник, в котором объясняется, как создать новый лес Active Directory на виртуальной машине в виртуальной сети Windows Azure." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Установка нового леса Active Directory в Windows Azure" authors=""  solutions="" writer="" manager="" editor=""  />




#Установка нового леса Active Directory в Windows Azure

В этом учебнике даются пошаговые инструкции по созданию нового леса Active Directory на виртуальной машине в [виртуальной сети Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx). В рамках этого руководства считается, что виртуальная сеть виртуальной машины не подключена к корпоративной сети вашей компании. Ориентирующее руководство по установке доменных служб Active Directory (AD DS) в виртуальной сети Windows Azure см. в разделе [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx).

##Оглавление##

* [Предварительные требования](#Prerequisites)
* [Шаг 1. Создание первой виртуальной машины](#Step1)
* [Шаг 2. Присоединение дополнительных дисков к виртуальной машине](#Step2)
* [Шаг 3. Установка доменных служб Active Directory](#Step3)
* [Шаг 4. Проверка установки](#Step4)
* [Шаг 5. Архивация контроллера домена](#Step5)
* [Шаг 6. Подготовка виртуальной машины, присоединенной к домену при запуске](#Step6)


<h2><a id="Prerequisites"></a>Предварительные требования</h2>

Перед установкой доменных служб Active Directory (AD DS) на виртуальной машине Windows Azure необходимо создать виртуальную сеть, используя один из следующих вариантов.

-	**Создайте виртуальную сеть *без* подключения к другой сети,** выполнив следующие действия в указанном порядке: 
1.  Сначала [создайте виртуальную сеть в Windows Azure](http://www.windowsazure.com/ru-ru/manage/services/networking/create-a-virtual-network/). 
2.  Затем установите службы AD DS, следуя пошаговым инструкциям, приведенным далее в учебнике. 
	
-	**Создайте виртуальную сеть, *имеющую* подключение к другой сети**, например к среде Active Directory на локальном объекте. Для этого выполните следующие действия в указанном порядке: 

1.	Сначала [создайте виртуальную сеть для подключения между площадками](../cross-premises-connectivity/). 
2.	Затем [добавьте виртуальную машину в виртуальную сеть](../add-a-vm-to-a-virtual-network/). 
3.	Наконец, установите службы AD DS, выполнив пошаговые инструкции в разделе [Установка реплики контроллера домена Active Directory в виртуальных сетях Windows Azure](../replica-domain-controller/).

<h2><a id="Step1"></a>Шаг 1. Создание первой виртуальной машины</h2>

Для создания первой виртуальной машины можно использовать портал управления Windows Azure или Windows Azure PowerShell.

<h3>Создание виртуальной машины с помощью портала управления Windows Azure</h3>
1.	Щелкните последовательно **Создать**, **Среда выполнения приложений**, **Виртуальная машина** и **Из коллекции**. 

	![Создание виртуальной машины из коллекции][create-vm]

2.	Выберите образ.

	![Образ виртуальной машины][vm-image]

3.	Введите имя новой виртуальной машины, имя пользователя и пароль, затем выберите дату выпуска версии и размер.

	![Конфигурация виртуальной машины][vm-configuration]

4.	Выберите виртуальную сеть и подсеть, затем примите остальные значения по умолчанию.

	![Подсеть виртуальной машины][vm-subnet]

5.	Примите конечные точки по умолчанию и щелкните флажок для завершения работы мастера.

	![Порты виртуальной машины][vm-ports]

<h3>Создание виртуальной машины с помощью Windows Azure PowerShell</h3>

1.	Создайте учетную запись хранения, которая находится в одном регионе с территориальной группой. Чтобы проверить регион территориальной группы, щелкните **Сети** и выберите **Территориальные группы**. Чтобы создать учетную запись хранения, сделайте следующее: 

	а. Последовательно щелкните **Службы данных **, **Хранилище** и **Быстрое создание**. 

	б. В разделе URL введите имя учетной записи хранения, а в поле **Регион или территориальная группа** выберите регион территориальной группы, например: "США, запад". Выбор региона для учетной записи хранения позволяет использовать учетную запись с любой территориальной группой в виртуальной сети.

2.	Установите [Windows Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx). Виртуальная машина, на которой планируется установить службы AD DS, должна быть создана с помощью Windows Azure PowerShell, что позволит сохранить параметры DNS-клиента контроллера домена после восстановления службы. Для установки Windows Azure PowerShell требуется установить Microsoft .NET Framework 4.5 и Windows Management Framework 3.0. Если они еще не установлены, компьютер потребуется перезапустить, чтобы завершить процесс установки. 

	а. Перейдите на веб-страницу [https://www.windowsazure.com/ru-ru/](https://www.windowsazure.com/ru-ru/).

	б. Последовательно щелкните **Загрузки**, **Программы командной строки** и **Windows Azure PowerShell**.

	в. Щелкните **Выполнить**. Щелкните **Да**, если появляется соответствующий запрос диалогового окна **Контроль учетных записей**. 

	г. Щелкните **Установить**, чтобы выполнить мастер установки, щелкните **Принимаю**, а после завершения работы мастера нажмите кнопку **Готово**. 

	д.	Щелкните **Выход**, чтобы закрыть установщик веб-платформы версии 4.0.

3.	При работе в Windows 7 щелкните **Пуск**, выберите **Все программы**, затем **Windows Azure**, щелкните правой кнопкой мыши **Windows Azure PowerShell** и выберите **Запуск от имени администратора**. Щелкните **Да**, если появляется соответствующий запрос диалогового окна **Контроль учетных записей**. При работе в Windows 8 щелкните **Пуск**, затем в поле **Поиск** введите Windows Azure PowerShell и нажмите клавишу ВВОД. 

4.	В Windows Azure PowerShell выполните следующий командлет, а затем введите **Д** для выполнения команды.

		Set-ExecutionPolicy RemoteSigned

5.	Выполните следующий командлет:

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1'

6.	Выполните следующий командлет:

		Get-AzurePublishSettingsFile

	Появится запрос входа на портал Windows Azure, после чего появится запрос на сохранение файла .publishsettings. Сохраните файл, например в каталоге E:\PowerShell\MyAccount.publishsettings. В дальнейшем для выполнения любого другого командлета Windows Azure PowerShell не нужно будет повторять шаги с 4 по 6, поскольку их необходимо выполнить только один раз. 


7.	Выполните следующий командлет для открытия ISE Windows Azure PowerShell:

		powershell ise

8.	Вставьте следующий скрипт в ISE Windows Azure PowerShell, заменив местозаполнители (например, *subscriptionname*) собственными значениями. Затем выполните скрипт. При необходимости щелкните **Сети** на портале управления, чтобы получить название подписки. Имя учетной записи хранения — это имя, указанное на шаге 1. В следующем скрипте используется имя образа, устанавливающего Windows Server 2012, однако имена образов периодически обновляются. Чтобы получить список доступных в настоящее время образов, выполните командлет <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152878.aspx">Get-AzureVMImage</a>. Виртуальные сети Windows Azure поддерживают меры безопасности для виртуализованных контроллеров домена, представленные начиная с Windows Server 2012. Дополнительные сведения о мерах безопасности для виртуализованных контроллеров домена см в разделе <a href="http://technet.microsoft.com/ru-ru/library/hh831734.aspx">Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)</a>. 

		cls
		
	    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
	    Import-AzurePublishSettingsFile 'C:\PowerShell\Justinha001.publishsettings'
	    Set-AzureSubscription -SubscriptionName 'Networking Demo Subscription' -CurrentStorageAccount 'yourstorageaccount'
	    Select-AzureSubscription -SubscriptionName 'Networking Demo Subscription'
	
	    #Deploy the Domain Controller in a virtual network
	    #-------------------------------------------------
	
		#Specify my DC's DNS IP (127.0.0.1)
		$myDNS = New-AzureDNS -Name 'myDNS' -IPAddress '127.0.0.1'
		$vmname = 'ContosoDC1'
		# OS Image to Use
		$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201306.01-en.us-127GB.vhd'
		$service = 'ConDC1demosvc'
        $AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
	
		#VM Configuration
		$MyDC = New-AzureVMConfig -name $vmname -InstanceSize 'Small' -ImageName $image |
			Add-AzureProvisioningConfig -Windows -Password P@$$w0rd |
				Set-AzureSubnet -SubnetNames 'BackEndSubnet'

		New-AzureVM -ServiceName $service -VMs $MyDC -AffinityGroup $AG -DnsSettings $myDNS -VNetName $vnet

	В случае повторного выполнения скрипта необходимо предоставить уникальное значение переменной $service. Можно выполнить командлет Test-AzureName -Service <i>имя службы</i>, результат которого показывает, используется ли уже это имя. После успешного выполнения командлета Windows Azure PowerShell виртуальная машина появится в пользовательском интерфейсе портала управления в остановленном состоянии. После этого необходимо начать процедуру подготовки. После подготовки виртуальной машины выполните дальнейшие действия.

<h2><a id="Step2"></a>Шаг 2. Присоединение дополнительных дисков к виртуальной машине</h2>

1.	На портале управления щелкните имя созданной виртуальной машины, затем в нижней части экрана щелкните **Присоединить** и **Присоединить пустой диск**.


2. Введите нужный размер жесткого диска (в ГБ), например 30. 

	![Указание размера диска][specify-disk-size]

3.	Повторите шаги 9 и 10, чтобы присоединить второй диск.


4.	Щелкните имя виртуальной Машины, затем щелкните **Подключить**.

	![Подключение][connect]


5.	Щелкните **Открыть**.

	![Открытость][open]

6.	В диалоговом окне подключения по RDP щелкните **Больше не выводить запрос о подключениях к этому компьютеру**, затем щелкните **Подключить**.

	![Подключение по RDP][connect-rdp]

7.	Введите учетные данные в формате <i>имя виртуальной машины</i>\<i>имя администратора</i>. Если виртуальная машина подготовлена с помощью Windows Azure PowerShell, введите <i>имя виртуальной машины</i>\Administrator.

	![Учетные данные][credentials]


8. В окне подключения к удаленному рабочему столу щелкните **Да**.

	![Удаленный рабочий стол][remote-desktop]



<h2><a id="Step3"></a>Шаг 3. Установка доменных служб Active Directory</h2>

1.	Инициализируйте диск, присоединенный к виртуальной машине, и создайте новый том для хранения файлов Active Directory. 

	а. 	В диспетчере сервера щелкните **Файловые службы и службы хранилища**. 

	б.	Щелкните **Диски**, щелкните правой кнопкой мыши присоединенный диск, щелкните **Инициализировать**, затем щелкните **Да** для подтверждения операции.

	c.	После завершения инициализации щелкните правой кнопкой мыши диск и выберите **Создать том**. Примите значения по умолчанию в мастере создания нового тома и завершите создание тома. Затем создайте новую папку с именем **NTDS** для хранения базы данных Active Directory и файлов журналов.

2.	В диспетчере сервера щелкните **Управление** и **Добавить роли и компоненты**, чтобы запустить мастер добавления ролей. Дополнительные сведения об установке служб AD DS на Windows Server 2012 см. в разделе [Установка доменных служб Active Directory (уровень 100)](http://technet.microsoft.com/ru-ru/library/hh472162.aspx).


	![Добавление роли][add-role]


3.	На странице "Перед началом работы" нажмите кнопку **Далее**.


4.	На странице выбора типа установки щелкните **Установка ролей или компонентов** и нажмите кнопку **Далее**.

	![Выбор типа установки][select-installation]


5.	На странице выбора целевого сервера щелкните **Выберите сервер из пула серверов**, щелкните имя сервера и нажмите кнопку **Далее**.

	![Выбор сервера][select-server]

6.	На странице выбора ролей сервера щелкните **Доменные службы Active Directory**, затем в диалоговом окне мастера добавления ролей и компонентов щелкните **Добавить компоненты** и нажмите кнопку **Далее**.  

	![Выбор роли сервера][select-server-role]

	![Добавление средств][add-tools]


7.	На странице выбора компонентов выберите все дополнительные компоненты, которые хотите установить, и нажмите кнопку **Далее**. 

	![Выбор компонентов][select-features]


8.	На странице доменных служб Active Directory просмотрите информацию и нажмите кнопку **Далее**.

9.	На странице подтверждения выбранных параметров установки щелкните **Установить**.

	![Подтверждение роли сервера][confirm-server-role]

10.	На странице результатов убедитесь в успешном завершении установки и щелкните **Повысить роль этого сервера до уровня контроллера домена**, чтобы запустить мастер конфигурации доменных служб Active Directory.

	![Повышение роли][promote]
	
	Примечание. Если закрыть мастер добавления ролей на этом этапе, не запуская мастер конфигурации доменных служб Active Directory, то затем можно перезапустить его, щелкнув "Задачи" в диспетчере сервера.

11.	На странице конфигурации развертывания щелкните **Добавить новый лес** и введите имя корневого домена (например, fabrikam.com), затем нажмите кнопку **Далее**.

	![Новый лес][new-forest]

12.	На странице параметров контроллера домена введите и подтвердите пароль режима восстановления служб каталогов, примите остальные значения по умолчанию и нажмите кнопку **Далее**.

	![Параметры контроллера домена][dc-options]

13.	На странице параметров DNS (которая появляется только при установке DNS-сервера) щелкните **Создать DNS-делегирование**, если это необходимо, и нажмите кнопку **Далее**. Если выбрали этот параметр, введите учетные данные, имеющие разрешение на создание записей DNS-делегирования в родительской зоне DNS. Если не удается связаться с DNS-сервером, на котором размещается родительская зона, параметр **Создать DNS-делегирование** недоступен.

14.	На странице дополнительных параметров введите новое имя NetBIOS или проверьте имя домена NetBIOS по умолчанию, затем нажмите кнопку **Далее**.

	![Дополнительные варианты][additional-options]

15.	На странице путей введите или выберите расположения для базы данных Active Directory, файлов журналов и папки SYSVOL, затем нажмите кнопку **Далее**. 	
	
	![Пути][paths]

16.	На странице обзора параметров проверьте выбранные элементы, затем нажмите кнопку **Далее**. 

	![Обзор параметров][review-options]

17.	На странице проверки предварительных требований убедитесь, что проверка предварительных требований завершена, затем щелкните **Установить**. 

	![Проверка предварительных требований][prereq-check]

18.	На странице результатов убедитесь, что сервер успешно настроен как контроллер домена. Сервер будет автоматически перезапущен для завершения установки служб AD DS. 

<h2><a id="Step4"></a>Шаг 4. Проверка установки</h2>

1.	Повторно подключитесь к виртуальной машине.

2.	Щелкните **Пуск**, введите **Командная строка**, щелкните значок командной строки, затем щелкните **Запуск от имени администратора**. 

3.	Введите следующую команду и нажмите клавишу ВВОД:

		Dcdiag /c /v

4.	Убедитесь, что тесты выполнены успешно. Некоторые тесты, связанные с проверкой IP-адресов, могут завершиться с ошибкой. Во избежание ошибок проверки, связанных с сервером времени, [настройте службу времени Windows](http://technet.microsoft.com/ru-ru/library/cc731191.aspx) на новом контроллере домена.


<h2><a id="Step5"></a>Шаг 5. Архивация контроллера домена</h2>

1.	Подключитесь к виртуальной машине.

2.	В диспетчере сервера щелкните **Добавить роли и компоненты** и на странице выбора компонентов щелкните **Система архивации данных Windows Server**. Следуйте инструкциям по установке системы архивации данных Windows Server.

3.	Последовательно щелкните **Пуск**, **Администрирование**, **Система архивации данных Windows Server** и **Однократная архивация**.

4.	Щелкните **Другие параметры** и нажмите кнопку **Далее**.

5.	Щелкните **Весь сервер**, затем нажмите кнопку **Далее**.

6.	Щелкните **Локальные диски**, затем нажмите кнопку **Далее**.

7.	Выберите диск назначения, который не содержит файлы операционной системы или базу данных Active Directory, затем нажмите кнопку **Далее**.
    ![Выполните архивацию контроллера домена][backup-dc]

8.	Подтвердите выбранные параметры и щелкните **Архивация**. 


<h2><a id="Step6"></a>Шаг 6. Подготовка виртуальной машины, присоединенной к домену при запуске</h2>
После настройки контроллера домена можно создать виртуальные машины с помощью портала управления или запустить приведенный ниже скрипт Windows PowerShell для подготовки дополнительных виртуальных машин и их автоматического присоединения к домену после подготовки. Параметры сопоставителя DNS-клиента для виртуальной машины можно настроить при подготовке виртуальных машин.  

При создании виртуальных машин с помощью портала управления укажите внутренний IP-адрес контроллера домена в качестве предпочитаемого DNS-сервера в параметрах сопоставителя DNS-клиента. Чтобы определить внутренний IP-адрес контроллера домена, щелкните имя виртуальной машины, на которой он работает.

Дополнительные сведения об использовании Windows PowerShell см. в разделах [Начало работы с Windows Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx) и [Командлеты для управления Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841).

1.	Чтобы использовать Windows Azure PowerShell для создания дополнительной виртуальной машины, присоединенной к домену при первом запуске, откройте ISE Windows Azure PowerShell, вставьте в окно следующий скрипт, замените местозаполнители (например, *ContosoDC13*) собственными значениями и выполните этот скрипт. Укажите имя контроллера домена для параметра -Name командлета New-AzureDNS. 

	В следующем примере контроллер домена имеет внутренний IP-адрес 10.4.3.1. Командлет Add-AzureProvisioningConfig также принимает параметр -MachineObjectOU (указывается в виде полного доменного имени в Active Directory), который позволяет настраивать параметры групповых политик на всех виртуальных машинах этого контейнера.

	После подготовки виртуальных машин выполните вход, указав доменную учетную запись в формате UPN, например: administrator@corp.contoso.com. 

		cls
		
	    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
	    Import-AzurePublishSettingsFile '*E:\PowerShell\MyAccount.publishsettings*'
	    Set-AzureSubscription -SubscriptionName *subscriptionname* -CurrentStorageAccount *storageaccountname*
	    Select-AzureSubscription -SubscriptionName *subscriptionname*
		
		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name '*ContosoDC13*' -IPAddress '*10.4.3.1*'
		
		# OS Image to Use
		$image = 'fb83b3509582419d99629ce476bcb5c8__SQL-Server-2012SP1-CU5-11.0.3373.0-Enterprise-ENU-Win2012'
		$service = '*myazuresvcindomainM1*'
		$AG = '*YourAffinityGroup*'
		$vnet = '*YourVirtualNetwork*'
		$pwd = '*p@$$w0rd*'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain '*corp*' -DomainPassword '*p@$$w0rd*' -DomainUserName 'Administrator' -JoinDomain '*corp.contoso.com*'|
		    Set-AzureSubnet -SubnetNames '*BackEnd*'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet

В случае повторного выполнения скрипта необходимо предоставить уникальное значение переменной $service. Можно выполнить командлет Test-AzureName -Service <i>имя службы</i>, результат которого показывает, используется ли уже это имя. После успешного выполнения командлета Windows Azure PowerShell виртуальные машины появятся в пользовательском интерфейсе портала управления в остановленном состоянии. После этого необходимо начать процедуру подготовки. После подготовки виртуальных машин можно выполнить вход на них.		

## См. также

-  [Виртуальная сеть Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx)

-  [Windows Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx)

-  [Командлеты управления Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841)

-  [Введение в виртуализацию доменных служб Active Directory (AD DS) (уровень 100)](http://technet.microsoft.com/ru-ru/library/hh831734.aspx)

[create-vm]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMFromGallery.png
[vm-image]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMImageSelection.png
[vm-configuration]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfig.png
[vm-subnet]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfigSubnet.png
[vm-ports]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfigPorts.png
[specify-disk-size]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMAttachDisk.png
[connect]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConnect.png
[open]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMOpenRDP.png
[connect-rdp]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConnectRDP.png
[credentials]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMCreds.png
[remote-desktop]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMRDPCert.png
[add-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAddRole.png
[select-installation]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectInstallationType.png
[select-server]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectDestinationServer.png
[select-server-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectServerRole.png
[add-tools]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAddTools.png
[select-features]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectFeatures.png
[confirm-server-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCConfirmRole.png
[promote]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPromote.png
[new-forest]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCNewForest.png
[dc-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCOptions.png
[additional-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAdditionalOptions.png
[paths]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPaths.png
[review-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCReviewOptions.png
[prereq-check]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPrereqCheck.png
[backup-dc]: ./media/active-directory-new-forest-virtual-machine/BackupDC.png



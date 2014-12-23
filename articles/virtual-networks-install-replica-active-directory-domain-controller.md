<properties urlDisplayName="Replica domain controller" pageTitle="Установка реплики контроллера домена в Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Установка реплики контроллера домена Active Directory в виртуальных сетях Azure

В данном пошаговом учебнике перечислены действия, которые следует выполнить для установки дополнительного контроллера домена из леса Active Directory вашей организации на виртуальной машине (ВМ) в [виртуальной сети Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx). В рамках этого руководства считается, что виртуальная сеть виртуальной машины подключена к корпоративной сети вашей компании. Руководство по основам установки доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в разделе [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156090.aspx).

##Оглавление ##

* [Предварительные требования](#Prerequisites)
* [Шаг 1: проверка статического IP-адреса для основного контроллера домена](#verifystaticip)
* [Шаг 2: установка леса предприятия](#installforest)
* [Шаг 3: создание подсетей и сайтов](#subnets)
* [Шаг 4: установка дополнительного контроллера домена на CloudSite](#cloudsite)
* [Шаг 5: Проверка установки](#validate)
* [Шаг 6: Подготовка виртуальной машины, присоединенной к домену при запуске](#provisionvm)
* [Шаг 7: Выполните архивацию контроллера домена](#backup)
* [Шаг 8: тестирование аутентификации и авторизации](#test)


<h2><a id="Prerequisites"></a>Предварительные требования</h2>

-	[Настройте VPN "сеть-сеть" на портале управления](http://msdn.microsoft.com/ru-ru/library/dn133795.aspx), то есть между виртуальной сетью Azure и корпоративной сетью.
-	Создайте облачную службу в виртуальной сети.
-	Разверните две виртуальные машины в облачной службе, которые будут являться частью виртуальной сети (укажите подсеть, в которую следует поместить виртуальную машину). Дополнительные сведения см. в разделе [Добавление виртуальной машины к виртуальной сети](http://azure.microsoft.com/ru-ru/documentation/articles/virtual-networks-add-virtual-machine/). Одна виртуальная машина должна иметь размер L и больше, чтобы к ней можно было подсоединить два диска данных. Диски данных должны хранить следующее:
	- База данных и журналы Active Directory.
	- Резервные копии состояния системы.
-	Корпоративная сеть с двумя виртуальными машинами (основной контроллер домена и файловый сервер).
-	При необходимости предоставить внешним пользователям право получения имен учетных записей в Active Directory следует развернуть инфраструктуру DNS. В этом случае следует создать делегирование зоны DNS, прежде чем устанавливать DNS-сервер на контроллере домена или разрешить мастеру установки доменных служб Active Directory создать подобное делегирование. Дополнительные сведения о делегировании зон DNS см. в разделе [Делегирование зоны](http://technet.microsoft.com/library/cc753500.aspx).
-	На контроллере домена, который был установлен на виртуальной машине Azure, настройте параметры средства разрешения клиента DNS, как показано ниже.
	- Основной DNS-сервер: локальный DNS-сервер.
	- Дополнительный DNS-сервер: адрес замыкания на себя или, если возможно, другой DNS-сервер, работающий на контроллере домена в той же виртуальной сети.

<div class="dev-callout"> 
<b>Примечание.</b>
<p>Вам необходимо предоставить собственную инфраструктуру DNS для поддержки AD DS в виртуальной сети Azure. Инфраструктура DNS на базе Azure в рамках этого выпуска не поддерживает некоторые функции, необходимые для AD DS, такие как динамическая регистрация записей служб SRV. </p>
</div>

<div class="dev-callout"> 
<b>Примечание.</b>
<p>Если вы уже выполнили действия, приведенные в разделе <a href="/ru-ru/manage/services/networking/active-directory-forest/">Установка нового леса Active Directory в Azure</a>, то, возможно, потребуется удалить AD DS с контроллера домена в виртуальной сети Azure до начала работы с этим учебником. Дополнительные сведения об удалении AD DS см. в разделе <a href="http://technet.microsoft.com/ru-ru/library/cc771844(v=WS.10).aspx">Удаление контроллера домена из домена</a>.</p>
</div>


<h2><a id="verifystaticip"></a>Шаг 1: проверка статического IP-адреса для основного контроллера домена</h2>

1. Выполните вход на основной контроллер домена в корпоративной сети.

2. В диспетчере сервера щелкните пункт "Просмотр сетевых подключений".

3. Щелкните правой кнопкой мыши подключение к локальной сети и выберите пункт "Свойства".

4. Выберите пункт "Протокол Интернета версии 4 (TCP/IPv4)" и нажмите кнопку "Свойства".

5. Убедитесь, что серверу присвоен статический IP-адрес. 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>Шаг 2: установка леса предприятия</h2>

1. В сеансе RDP для виртуальной машины щелкните **Пуск**, наберите **dcpromo** и нажмите клавишу ВВОД.

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. На странице приветствия нажмите кнопку **Далее**.

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. На странице "Совместимость операционных систем" нажмите кнопку **Далее**.

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. На странице "Выберите конфигурацию развертывания" выберите пункт **Создать новый домен в новом лесу** и нажмите кнопку **Далее**. 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. На странице "Выберите конфигурацию развертывания" выберите пункт **corp.contoso.com**, полное доменное имя (FQDN) корневого домена леса, затем нажмите кнопку **Далее**. 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. На странице "Задание режима работы леса" выберите **Windows Server 2008 R2**, а затем нажмите кнопку **Далее**.

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. На странице "Дополнительные параметры контроллера домена" выберите **сервер DNS** и нажмите кнопку **Далее**.

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. При появлении предупреждения о делегировании DNS нажмите кнопку **Да**.

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. В разделе "Расположение" для базы данных Active Directory, файлов журнала и страницы SYSVOL введите или выберите расположение файлов и нажмите кнопку **Далее**.

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. На странице "Администратор восстановления служб каталога" введите и подтвердите пароль DSRM и нажмите кнопку **Далее**.

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. На странице "Сводка" подтвердите свой выбор и нажмите кнопку **Далее**. 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. После завершения работы мастера установки Active Directory нажмите кнопку **Готово** и щелкните **Перезагрузить сейчас** для завершения установки. 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>Шаг 3: создание подсетей и сайтов</h2>

1. На основном контроллере домена щелкните "Пуск", затем "Администрирование" и выберите "Active Directory - сайты и службы".
2. Выберите **Сайты**, щелкните правой кнопкой мыши пункт **Подсети**, а затем щелкните **Новая подсеть**.

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. В поле **Prefix::** введите **10.1.0.0/24**, выберите объект сайта **Default-First-Site-Name** и нажмите кнопку **ОК**.

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. Щелкните правой кнопкой мыши пункт **Сайты** и выберите пункт **Новый сайт**.

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. В поле "Имя" введите **CloudSite**, выберите **DEFAULTIPSITELINK** и нажмите кнопку **ОК**. 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. Нажмите кнопку **ОК** для подтверждения создания сайта. 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. Щелкните правой кнопкой мыши пункт **Подсети**, а затем щелкните **Новая подсеть**.

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. В поле **Prefix::** введите **10.4.2.0/24**, выберите объект сайта **CloudSite** и нажмите кнопку **ОК**.

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>Шаг 4: установка дополнительного контроллера домена на CloudSite</h2>

1. Зайдите на YourVMachine, щелкните **Пуск**, введите **dcpromo** и нажмите ВВОД.

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. На странице приветствия нажмите кнопку **Далее**.

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. На странице "Совместимость операционных систем" нажмите кнопку **Далее**.

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. На странице "Выбор конфигурации развертывания" щелкните пункт **Существующий лес**, выберите **Добавление контроллера домена к существующему домену** и нажмите кнопку **Далее**.

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. На странице "Сетевые учетные данные" убедитесь, что контроллер домена устанавливается для домена **corp.contoso.com** и ведите учетные данные представителя группы Domain Admins (Администраторы доменов) или воспользуйтесь учетными данными corp\administrator. 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. На странице "Выбор домена" нажмите кнопку **Далее**. 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. На странице "Выбор сайта" убедитесь, что выбран CloudSite, и нажмите кнопку **Далее**.

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. На странице "Дополнительные варианты контроллера домена" нажмите кнопку **Далее**. 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. В окне предупреждения о необходимости присвоения статического IP-адреса щелкните пункт **"Да", компьютер будет использовать динамически назначаемый DHCP-сервером IP-адрес (не рекомендуется)**

	**Важно** 

Хотя IP-адрес в виртуальной сети Azure является динамическим, срок его аренды равен сроку действия виртуальной машины. Поэтому нет необходимости задавать статический IP-адрес контроллера домена, устанавливаемого в виртуальной сети. Установка статического IP-адреса виртуальной машины приведет к появлению проблем со связью.


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. По запросу в окне предупреждения о делегировании DNS нажмите кнопку **Да**.

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. На странице "Местоположение базы данных, файлов журналов Active Directory и SYSVOL" нажмите кнопку "Обзор" и введите местоположение папки NTDS, созданной ранее на дополнительном диске с данными, затем нажмите кнопку **Далее**. 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. На странице "Администратор восстановления служб каталога" введите и подтвердите пароль DSRM и нажмите кнопку **Далее**.

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. На странице "Сводка" нажмите кнопку **Далее**.

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. После завершения работы мастера установки Active Directory нажмите кнопку **Готово** и щелкните **Перезагрузить сейчас** для завершения установки. 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>Шаг 5: Проверка установки</h2>

1. Повторно подключитесь к виртуальной машине.

2. Нажмите кнопку **Пуск**, щелкните правой кнопкой мыши пункт **Командная строка** и выберите **Запустить с правами администратора**. 

3. Введите следующую команду и нажмите "ВВОД":  'Dcdiag /c /v'

4. Убедитесь, что тесты выполнены успешно. 

После настройки контроллера домена запустите приведенный ниже командлет Windows PowerShell, чтобы подготовить дополнительные виртуальные машины для автоматического присоединения к домену во время подготовки. Настройки разрешения DNS-клиента для виртуальной машины должны быть настроены при подготовке виртуальных машин. Подставьте правильные имена для вашего домена, имени ВМ и т. п. 

Для получения дополнительных сведений об использовании Windows PowerShell см. [Начало работы с Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx) и [Командлеты управления Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841).


<h2><a id="provisionvm"></a>Шаг 6: Подготовка виртуальной машины, присоединенной к домену при запуске</h2>

1. Для создания дополнительной виртуальной машины с присоединением к домену при первом запуске откройте ISE Azure PowerShell, вставьте в окно следующий скрипт, замените заполненные поля собственными значениями и запустите сценарий. 

Чтобы определить внутренний IP-адрес контроллера домена, щелкните имя виртуальной сети, где работает этот контроллер. 

	В следующем примере внутренний IP-адрес контроллера домена задан как 10.4.3.1. Командлет Add-AzureProvisioningConfig также принимает параметр -MachineObjectOU (указывается в виде полного доменного имени в Active Directory), который позволяет настраивать параметры групповых политик на всех виртуальных машинах этого контейнера.

	После подготовки виртуальных машин выполните вход, указав доменную учетную запись в формате UPN, например: administrator@corp.contoso.com. 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-ru-ru-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>Шаг 7: Выполните архивацию контроллера домена</h2>


1. Подключитесь к своей виртуальной машине.

2. Нажмите кнопку **Пуск**, выберите пункт **Диспетчер серверов**, выберите **Добавить компоненты** и далее выберите **Компоненты системы архивации данных Windows Server**. Следуйте инструкциям по установке системы архивации данных Windows Server.

3. Нажмите кнопку **Пуск**, выберите пункт **Система архивации данных Windows Server**, щелкните **Однократная архивация**.
 
4. Щелкните **Различные варианты** и нажмите кнопку **Далее**.

5. Щелкните **Полный сервер** и нажмите кнопку **Далее**.

6. Щелкните **Локальные диски** и нажмите кнопку **Далее**.

7. Выберите диск назначения, который не содержит файлы операционной системы или базы данных Active Directory и нажмите кнопку "Далее".

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. Подтвердите свой выбор настроек системы архивации и нажмите кнопку **Архивация**.

<h2><a id="test"></a>Шаг 8: тестирование аутентификации и авторизации</h2>

1. С целью тестирования проверки подлинности и авторизации создайте учетную запись пользователя домена в Active Directory. 
Войдите в систему виртуальной машины на каждом сайте и создайте общую папку.

2. Проверьте доступ к общей папке, используя разные учетные записи, группы и разрешения. 

## См. также

-  [Виртуальная сеть Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx)

-  [Windows Azure IT Pro IaaS: (01) основы работы с виртуальной машиной](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT Pro IaaS: (05) создание виртуальных сетей и подключений между организациями](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156055.aspx)

-  [Командлеты управления Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841)

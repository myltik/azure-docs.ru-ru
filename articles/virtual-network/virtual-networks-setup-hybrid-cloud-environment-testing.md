<properties 
	pageTitle="Создание гибридной облачной среды для тестирования" 
	description="Учимся создавать гибридную облачную среду для ИТ-специалистов или тестирования разработки." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Создание гибридной облачной среды для тестирования

В этом разделе описываются шаги по созданию гибридной облачной среды с помощью Microsoft Azure для тестирования. Это конфигурация, которая получается в результате.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

Данная конфигурация имитирует реальную рабочую гибридную среду из вашего местоположения в Интернете В ее состав входит:

-  упрощенная локальная сеть (подсеть Corpnet);
-  виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET);
-  VPN-подключение типа "сеть-сеть";
-  дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

-  разрабатывать и тестировать приложения в гибридной облачной среде.
-  создавать конфигурации тестов компьютеров в подсети Corpnet и в виртуальной сети TestVNET для гибридных облачных рабочих нагрузок ИТ-среды.

Существует пять основных этапов настройки гибридной облачной тестовой среды.

1.	Настройка компьютеров в подсети Corpnet.
2.	Настройка RRAS1.
3.	Создание виртуальной сети Azure между организациями.
4.	Создание VPN-подключения «сайт-сайт».
5.	Настройка DC2. 

Если у вас еще нет подписки Azure, вы можете зарегистрироваться для получения бесплатной пробной версии на веб-сайте [Try Azure](http://azure.microsoft.com/pricing/free-trial/). При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE]Виртуальные машины и шлюзы виртуальных сетей в Azure создают текущие расходы во время своей работы. На эти затраты выставляется счет при использовании бесплатной пробной версии, подписки MSDN или платной подписки. Чтобы снизить затраты на работу этой тестовой среды, когда она не используется, ознакомьтесь с разделом [Минимизация текущих расходов, связанных с этой средой](#costs).

Для этой конфигурации необходима тестовая подсеть (до четырех компьютеров), подключенная непосредственно к Интернету с общедоступным IP-адресом. Если у вас нет этих ресурсов, вы также можете [настроить имитацию гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md). Для имитации тестовой гибридной облачной среды необходима только подписка Azure.

## Этап 1. Настройка компьютеров в подсети Corpnet.

Воспользуйтесь инструкциями из раздела «Этапы настройки подсети Corpnet» [руководства по практической работе «Базовая конфигурация Windows Server 2012 R2»](http://www.microsoft.com/download/details.aspx?id=39638) для настройки компьютеров DC1, APP1 и CLIENT1 в подсети с именем Corpnet. **Эта подсеть должна быть изолирована от сети организации, поскольку она будет подключена прямо к Интернету через компьютер RRAS1.**

Далее войдите в DC1 с учетными данными CORP\User1. Для настройки домена CORP таким образом, чтобы компьютеры и пользователи использовали свой локальный контроллер домена для проверки подлинности, выполните следующие команды из командной строки Windows PowerShell с правами администратора.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET

Это текущая конфигурация.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## Этап 2. Настройка RRAS1.

RRAS1 обеспечивает маршрутизацию трафика и службы VPN-устройства между компьютерами подсети Corpnet и виртуальной сетью TestVNET. RRAS1 должен иметь два сетевых адаптера.

Сначала установите операционную систему на RRAS1.

1.	Начните с установки Windows Server 2012 R2.
2.	Следуйте инструкциям для завершения установки, указав надежный пароль для учетной записи локального администратора. Войдите в систему с помощью учетной записи локального администратора.
3.	Подключите RRAS1 к сети, которая имеет доступ к Интернету, и выполните обновление Windows, чтобы установить последние обновление для Windows Server 2012 R2.
4.	Один сетевой адаптер подключите к подсети Corpnet, а второй — прямо к Интернету. RRAS1 может находиться позади брандмауэра Интернета, но не должен быть позади транслятора сетевых адресов (NAT).

Далее настройте свойства TCP/IP у RRAS1. Для этого потребуется конфигурация общедоступного IP-адреса, в том числе адрес, маска подсети (или длина префикса), шлюз по умолчанию и DNS-серверы поставщика услуг Интернета ISP).

Используйте следующие команды в командной строке Windows PowerShell с правами администратора на RRAS1. Перед выполнение этих команд заполните значения переменных и удалите символы < and >. Текущие имена сетевых адаптеров можно увидеть в результатах, возвращаемых командой **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter –Name $corpnetAdapterName –NewName Corpnet
	Rename-NetAdapter –Name $internetAdapterName –NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength –DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
	New-NetFirewallRule –DisplayName “Allow ICMPv4-Out” –Protocol ICMPv4 –Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

При выполнении последней команды убедитесь в получении четырех ответов с IP-адреса 10.0.0.1.

Это текущая конфигурация.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## Этап 3. Создание виртуальной сети Azure между организациями.

Сначала войдите на [портал управления Azure](https://manage.windowsazure.com/) с учетными данными своей подписки Azure и создайте виртуальную сеть под названием TestVNET.

1.	На портале управления Azure на панели задач последовательно выберите **Создать > Сетевые службы > Виртуальная сеть > Настраиваемое создание**.
2.	На странице «Сведения о виртуальной сети» введите **TestVNET** в поле **Имя**.
3.	В разделе **Расположение** выберите подходящий центр обработки данных для своего региона.
4.	Нажмите стрелку Далее.
5.	На странице «DNS-серверы и подключение VPN» в разделе **DNS-серверы** введите **DC1** в поле **Выбрать или ввести имя**, введите **10.0.0.1** в поле **IP-адрес**, а затем выберите **Настроить VPN типа «сеть-сеть»**.
6.	В разделе **Локальная сеть** выберите **Указать новую локальную сеть**. 
7.	Нажмите стрелку Далее.
8.	На странице «Подключение сайт-сайт»:
	- В поле **Имя** введите **Corpnet**. 
	- В поле **IP-адрес устройства VPN** введите общедоступный IP-адрес, назначенный веб-интерфейсу RRAS1.
	- В поле **Адресное пространство** в столбце **Начальный IP-адрес** введите **10.0.0.0**. В поле **CIDR (количество адресов)** выберите **/8**, а затем щелкните **Добавить адресное пространство**.
9.	Нажмите стрелку Далее.
10.	На странице « Адресные пространства виртуальной сети»:
	- В поле **Адресное пространство** в столбце **Начальный IP-адрес** выберите **192.168.0.0**.
	- В разделе **Подсети** щелкните **Subnet-1** и замените имя на **TestSubnet**. 
	- В столбце **CIDR (количество адресов)** для сети TestSubnet выберите **/24 (256)**.
	- Щелкните **Добавить подсеть шлюза**.
11.	Щелкните значок Завершить. Дождитесь создания виртуальной сети прежде, чем продолжить.

Затем установите Azure PowerShell на локальном компьютере с помощью инструкций из статьи [Установка и настройка Azure PowerShell](../install-configure-powershell.md).

После этого создайте новую облачную службу для виртуальной сети TestVNET. Необходимо выбрать уникальное имя. Например, службу можно назвать TestVNET-*UniqueSequence*, где *UniqueSequence* — это сокращенное название вашей организации. К примеру, если ваша организация называется Tailspin Toys, облачную службу можно назвать TestVNET-Tailspin.

Уникальность имени можно проверить, выполнив эту команду Azure PowerShell на локальном компьютере.

	Test-AzureName -Service <Proposed cloud service name>

Если команда возвращает значение «False»,имя является уникальным. Создайте облачную службу с помощью этой команды.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Далее создайте новую учетную запись хранения для виртуальной сети TestVNET. Необходимо выбрать уникальное имя. Можно проверить уникальность имени учетной записи хранения с помощью этой команды.

	Test-AzureName -Storage <Proposed storage account name>

Если команда возвращает значение «False»,имя является уникальным. Создайте и настройте учетную запись хранения с помощью этих команд.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Это текущая конфигурация.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## Этап 4. Создание VPN-подключения типа «сеть-сеть»

Сначала необходимо создать шлюз виртуальной сети.

1.	В портале управления Azure на локальном компьютере щелкните **Сети** в левой панели, а затем убедитесь, что в столбце **Состояние** для TestVNET указано значение **Создана**.
2.	Щелкните **TestVNET**. На странице панели мониторинга должно отображаться состояние **Шлюз не был создан**.
3.	На панели задач щелкните **Создать шлюз**, а затем щелкните **Динамическая маршрутизация**. Щелкните **Да** при появлении запроса. Подождите, пока шлюз не будет создан и его состояние не изменится на **Подключение**. Это может занять несколько минут.
4.	На странице панели мониторинга обратите внимание на **IP-адрес шлюза**. Это общий IP-адрес шлюза Azure VPN для виртуальной сети TestVNET. Данный IP-адрес понадобится для настройки RRAS1.
5.	На панели задач щелкните **Управление ключами**, а затем щелкните значок копирования рядом с ключом, чтобы скопировать его в буфер обмена. Вставьте этот ключ в документ и сохраните его. Значение этого ключа необходимо для настройки RRAS1. 

После этого настройте RRAS1 с помощью службы маршрутизации и удаленного доступа для работы в качестве устройства VPN для подсети Corpnet. Войдите на RRAS1 учетной записью локального администратора и выполните следующие команды в командной строке Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Далее настройте RRAS1 для получения VPN-подключения сайт-сайт от шлюза Azure VPN. Перезапустите RRAS1, а затем войдите в систему как локальный администратор и выполните следующие команды в командной строке Windows PowerShell. Необходимо указать IP-адрес шлюза Azure VPN и значение ключа.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Затем перейдите на портал управления Azure на локальном компьютере и подождите, пока для виртуальной сети TestVNET не отобразится состояние **Подключена**.

Теперь настройте RRAS1 для поддержки транслируемого трафика в сети Интернет. На RRAS1:

1.	На начальном экране введите **rras**, а затем щелкните **Маршрутизация и удаленный доступ**. 
2.	В дереве консоли откройте имя сервера и щелкните **IPv4**.
3.	Щелкните правой кнопкой мыши **Общие**, а затем нажмите кнопку **Новый протокол маршрутизации**.
4.	Щелкните **NAT**, а затем нажмите кнопку **OK**.
5.	В дереве консоли щелкните правой кнопкой мыши **NAT**, нажмите кнопку **Новый интерфейс**, щелкните **Corpnet**, а затем нажмите кнопку **ОК** два раза.
6.	Щелкните правой кнопкой мыши **NAT**, нажмите кнопку **Новый интерфейс**, щелкните **Интернет**, а затем нажмите кнопку **ОК**.
7.	На вкладке **NAT** нажмите кнопку **Общий интерфейс подключен к Интернету**, выберите **Включить NAT на данном интерфейсе**, а затем нажмите кнопку **ОК**.


Далее нужно настроить DC1, APP1 и CLIENT1 для использования RRAS1 в качестве шлюза по умолчанию.
 
На компьютере DC1 выполните следующие команды в командной строке Windows PowerShell с правами администратора.

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue –Router 10.0.0.2

Если имя интерфейса не Ethernet, используйте команду **Get-NetAdapter**, чтобы определить имя интерфейса.

На компьютере APP1 выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2

На компьютере CLIENT1 выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	ipconfig /renew

Это текущая конфигурация.
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## Этап 5. Настройка DC2

Во-первых, необходимо создать виртуальную машину Azure для DC2 с помощью выполнения следующих команд в командной строке Azure PowerShell на локальном компьютере.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


Далее выполните вход в новую виртуальную машину DC2.

1.	На левой панели портала управления Azure щелкните **Виртуальные машины**, а затем выберите **Работающие** в столбце **Состояние** для DC2.
2.	На панели задач щелкните **Подключиться**. 
3.	Когда будет предложено открыть файл DC2.rdp, щелкните **Открыть**.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
	- Имя: **DC2\**[имя учетной записи локального администратора]
	- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Да**.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на DC2 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1. Это проверка трафика через VPN-подключение сайт-сайт.

Далее следует добавить дополнительный диск данных как новый том с буквой диска F:.

1.	В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2.	В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3.	Щелкните **Задачи**, а затем **Новый том**.
4.	На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5.	На странице «Выбор сервера и диска» щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6.	На странице «Выбор размера тома» щелкните **Далее**.
7.	На странице «Назначение букве диска или папке» щелкните **Далее**.
8.	На странице «Выбор параметров файловой системы» щелкните **Далее**.
9.	На странице «Подтверждение выбора» щелкните **Создать**.
10.	После завершения нажмите кнопку **Закрыть**.

Затем настройте DC2 в качестве реплики контроллера домена для домена corp.contoso.com. Выполните следующие команды из командной строки Windows PowerShell на DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Следует иметь в виду, что вам будет предложено ввести пароль CORP\User1 и пароль режима восстановления служб каталогов (DSRM), а также перезапустить DC2.

Отметим, что виртуальная сеть TestVNET имеет собственный DNS-сервер(DC2). Необходимо настроить виртуальную сеть TestVNET для использования этого DNS-сервера.

1.	На левой панели портала управления Azure щелкните **Сети**, а затем выберите **TestVNET**.
2.	Нажмите **Настроить**.
3.	В разделе **DNS-серверы** удалите запись **10.0.0.1**.
4.	В разделе **DNS-серверы** добавьте запись с именем **DC2** и IP-адресом **192.168.0.4**. 
5.	На панели команд в нижней части экрана щелкните **Сохранить**.
6.	На левой панели портала управления Azure щелкните **Виртуальные машины**, а затем щелкните столбец **Состояние** рядом с DC2.
7.	На панели команд нажмите кнопку **Перезапустить**. Дождитесь пока DC2 не перезапустится.


Это текущая конфигурация.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
Гибридная облачная среда теперь готова для тестирования.

## Дополнительные ресурсы

[Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Настройка синхронизации каталогов (DirSync) Office 365 в гибридном облаке для тестирования](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Создание имитации гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Гибридные облачные тестовые среды Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Руководство по реализации служб инфраструктуры Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## Минимизация текущих расходов на такую среду

Чтобы свести к минимуму затраты на работающие виртуальные машины в этой среде, следует как можно быстрее выполнить необходимое тестирование и демонстрацию, а затем удалить или выключить виртуальные машины, если они не используются. Например, можно использовать автоматизацию Azure и Runbook для автоматического выключения виртуальных машин в виртуальной сети Test_VNET в конце каждого рабочего дня. Дополнительные сведения см. в статье [Начало работы со службой автоматизации Azure](../automation-create-runbook-from-samples.md).

Шлюз Azure VPN реализован как набор двух виртуальных машин Azure, работа которых создает текущие денежные расходы. Дополнительные сведения см.в статье [Цены на виртуальную сеть](http://azure.microsoft.com/pricing/details/virtual-network/). Чтобы свести к минимуму затраты на шлюз VPN, следует создать тестовую среду и как можно быстрее выполнить необходимое тестирование и демонстрацию или удалить шлюз, выполнив следующие действия.

1.	На портале управления Azure на локальном компьютере щелкните **Сети** на левой панели, выберите **TestVNET**, а затем нажмите кнопку **Панель мониторинга**.
2.	В панели задач щелкните **Удалить шлюз**. Щелкните **Да** при появлении запроса. Подождите, пока шлюз не будет удален и его состояние не изменится на **Шлюз не был создан**.

Если шлюз удаляется и необходимо восстановить эту тестовую среду, следует сначала создать новый шлюз.

1.	На портале управления Azure на локальном компьютере щелкните **Сети** на левой панели, а затем выберите **TestVNET**. На странице панели мониторинга должно отображаться состояние **Шлюз не был создан**.
2.	На панели задач щелкните **Создать шлюз**, а затем щелкните **Динамическая маршрутизация**. Щелкните **Да** при появлении запроса. Подождите, пока шлюз не будет создан и его состояние не изменится на **Подключение**. Это может занять несколько минут.
3.	На странице панели мониторинга обратите внимание на **IP-адрес шлюза**. Это новый общий IP-адрес шлюза Azure VPN для виртуальной сети TestVNET. Данный IP-адрес понадобится для перенастройки RRAS1.
4.	На панели задач щелкните **Управление ключами**, а затем щелкните значок копирования рядом с ключом, чтобы скопировать его в буфер обмена. Вставьте значение этого ключа в документ и сохраните его. Значение этого ключа необходимо для перенастройки RRAS1. 

Затем войдите на RRAS1 с правами локального администратора и выполните следующие команды в командной строке Windows PowerShell уровня администратора для перенастройки RRAS1 с использованием нового общедоступного IP-адреса и общего ключа.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

После этого перейдите на портал управления Azure на локальном компьютере и дождитесь, пока виртуальная сеть TestVNET не покажет подключенное состояние.
 

<!---HONumber=July15_HO4-->
<properties 
	pageTitle="Тестовая среда гибридного облака | Microsoft Azure" 
	description="Узнайте, как создать гибридную облачную среду для ИТ-специалистов или тестирования разработки, дополненной упрощенной локальной сетью." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/19/2016" 
	ms.author="josephd"/>

# Создание гибридной облачной среды для тестирования
 
В этом разделе описываются шаги по созданию гибридной облачной среды с помощью Microsoft Azure для тестирования. Это конфигурация, которая получается в результате.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

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
4.	Создание VPN-подключения "сайт-сайт".
5.	Настройка DC2.

Если у вас еще нет подписки Azure, вы можете зарегистрировать бесплатную учетную запись на странице [бесплатной ознакомительной версии Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас есть подписка MSDN или Visual Studio, ознакомьтесь с разделом [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Виртуальные машины и шлюзы виртуальных сетей в Azure создают текущие расходы во время своей работы. Шлюз Azure VPN реализован как набор двух виртуальных машин Azure. Дополнительные сведения см.в статье [Цены на виртуальную сеть](https://azure.microsoft.com/pricing/details/virtual-network/). Чтобы сократить расходы на использование VPN-шлюза, создайте тестовую среду и как можно быстрее выполните необходимые тестирования и демонстрации.

Для этой конфигурации необходима тестовая подсеть (до четырех компьютеров), подключенная непосредственно к Интернету с общедоступным IP-адресом. Если у вас нет этих ресурсов, вы также можете [настроить имитацию гибридной облачной среды для тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Для имитации тестовой гибридной облачной среды необходима только подписка Azure.

## Этап 1. Настройка компьютеров в подсети Corpnet.

Воспользуйтесь инструкциями из раздела "Этапы настройки подсети Corpnet" [руководства по практической работе "Базовая конфигурация Windows Server 2012 R2"](http://www.microsoft.com/download/details.aspx?id=39638) для настройки компьютеров DC1, APP1 и CLIENT1 в подсети с именем Corpnet. **Эта подсеть должна быть изолирована от сети организации, поскольку она будет подключена прямо к Интернету через компьютер RRAS1.**

Далее войдите в DC1 с учетными данными CORP\\User1. Для настройки домена CORP таким образом, чтобы компьютеры и пользователи использовали свой локальный контроллер домена для проверки подлинности, выполните следующие команды из командной строки Windows PowerShell с правами администратора.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## Этап 2. Настройка RRAS1.

RRAS1 обеспечивает маршрутизацию трафика и службы VPN-устройства между компьютерами подсети Corpnet и виртуальной сетью TestVNET. RRAS1 должен иметь два сетевых адаптера.

Сначала установите операционную систему на RRAS1.

1.	Начните с установки Windows Server 2012 R2.
2.	Следуйте инструкциям для завершения установки, указав надежный пароль для учетной записи локального администратора. Войдите в систему с помощью учетной записи локального администратора.
3.	Подключите RRAS1 к сети, которая имеет доступ к Интернету, и выполните обновление Windows, чтобы установить последние обновление для Windows Server 2012 R2.
4.	Один сетевой адаптер подключите к подсети Corpnet, а второй — прямо к Интернету. RRAS1 может находиться позади брандмауэра Интернета, но не должен быть позади транслятора сетевых адресов (NAT).

Далее настройте свойства TCP/IP у RRAS1. Для этого потребуется конфигурация общедоступного IP-адреса, в том числе адрес, маска подсети (или длина префикса), шлюз по умолчанию и DNS-серверы поставщика услуг Интернета ISP). На этапе 3 вам понадобится общедоступный IP-адрес.

Используйте следующие команды в командной строке Windows PowerShell с правами администратора на RRAS1. Перед выполнением этих команд заполните значения переменных и удалите знаки < и >. Текущие имена сетевых адаптеров можно увидеть в результатах, возвращаемых командой **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

При выполнении последней команды убедитесь в получении четырех ответов с IP-адреса 10.0.0.1.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## Этап 3. Создание виртуальной сети Azure между организациями.

Запустите командную строку Azure PowerShell.

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Войдите в свою учетную запись.

	Login-AzureRMAccount

Получите имя своей подписки, выполнив указанную ниже команду.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Настройте свою подписку Azure. Используйте ту же подписку, которая использовалась для построения базовой конфигурации. Замените все содержимое внутри кавычек, включая знаки < и >, правильными именами.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Затем создайте новую группу ресурсов для гибридной тестовой среды.

Чтобы вывести список существующих групп ресурсов и выбрать уникальное имя для новой группы, используйте эту команду.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Создайте группу ресурсов, выполнив следующие команды.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Для виртуальных машин на основе диспетчера ресурсов требуется учетная запись хранения на основе диспетчера ресурсов. Для каждой учетной записи хранения вам нужно выбрать глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureRMStorageAccount | Sort Name | Select Name

Создайте учетную запись хранения с помощью этих команд.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Затем создайте виртуальную сеть Azure, в которой будет размещена гибридная облачная среда, и защитите ее с применением группы безопасности сети.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

Создайте шлюзы для VPN-подключения типа «сеть — сеть», используя следующие команды. Вам понадобится общедоступный IP-адрес веб-интерфейса RRAS1, полученный на этапе 2.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

Имейте в виду, что процесс создания может занять 20 и более минут.

Затем используйте следующую команду для определения общедоступного IP-адреса VPN-шлюза Azure виртуальной сети.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

Запишите IP-адрес, отображенный в поле **IPAddress**. Он понадобится вам на этапе 4.

Затем получите случайный надежно зашифрованный 32-значный общий ключ у администратора сети или администратора безопасности. Вы также можете ознакомиться со статьей [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Создание случайной строки для предварительного ключа IPsec), в которой описывается получение предварительного ключа.

Создайте в Azure VPN-подключение типа "сеть — сеть", используя следующие команды.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## Этап 4. Создание VPN-подключения типа "сеть-сеть"

После этого настройте RRAS1 с помощью службы маршрутизации и удаленного доступа для работы в качестве устройства VPN для подсети Corpnet. Войдите на RRAS1 учетной записью локального администратора и выполните следующие команды в командной строке Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Далее настройте RRAS1 для получения VPN-подключения сайт-сайт от шлюза Azure VPN. Перезапустите RRAS1, а затем войдите в систему как локальный администратор и выполните следующие команды в командной строке Windows PowerShell. Необходимо указать общедоступный IP-адрес VPN-шлюза Azure и значение общего ключа, полученное на этапе 3.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Подождите несколько минут, пока между RRAS1 и VPN-шлюзом Azure установится подключение.

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

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Если имя интерфейса не Ethernet, используйте команду **Get-NetAdapter**, чтобы определить имя интерфейса.

На компьютере APP1 выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

На компьютере CLIENT1 выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	ipconfig /renew

Это текущая конфигурация.
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## Этап 5. Настройка DC2

Во-первых, необходимо создать виртуальную машину Azure для DC2 с помощью выполнения следующих команд в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем используйте портал Azure, чтобы подключиться к новой виртуальной машине DC2 с помощью учетных данных локального администратора.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на DC2 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1. Если используется *имитация гибридной облачной конфигурации*, то вы увидите четыре успешных ответа, полученных с IP-адреса 10.0.0.4. Это проверка трафика через VPN-подключение "сеть — сеть" или "виртуальная сеть — виртуальная сеть".

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

Следует иметь в виду, что вам будет предложено ввести пароль CORP\\User1 и пароль режима восстановления служб каталогов (DSRM), а также перезапустить DC2.

Отметим, что виртуальная сеть TestVNET имеет собственный DNS-сервер(DC2). Необходимо настроить виртуальную сеть TestVNET для использования этого DNS-сервера.

1.	В левой области портала Azure щелкните значок виртуальных сетей, а затем выберите **TestVNET**.
2.	На вкладке **Параметры** щелкните **DNS-серверы**.
3.	В поле **Основной DNS-сервер** введите **192.168.0.4**, чтобы заменить 10.0.0.4.
4.	Щелкните Сохранить.

После этого с помощью следующих команд в командной строке Azure PowerShell перезапустите DC2 для применения новой конфигурации DNS-сервера.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Гибридная облачная среда теперь готова для тестирования.
 
## Дальнейшие действия

- Настройка [фермы интрасети SharePoint ](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), [бизнес-приложения для Интернета](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) или [сервера синхронизации каталогов Office 365 (DirSync)](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) в этой среде.

<!---HONumber=AcomDC_0720_2016-->
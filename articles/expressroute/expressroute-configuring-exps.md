<properties
   pageTitle="Настройка ExpressRoute через поставщика Exchange"
   description="В этом учебнике показывается процесс настройки ExpressRoute через поставщиков Exchange (EXP)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  "Настройка подключения ExpressRoute через поставщика Exchange"

Чтобы настроить подключение ExpressRoute через поставщик Exchange, необходимо выполнить несколько шагов в соответствующем порядке. Эти инструкции помогут вам выполнять следующее.

- Создание каналов ExpressRoute и управление ими
- Настройка пиринга BGP для каналов ExpressRoute
- Связывание виртуальной сети с каналом ExpressRoute

##  Предварительные требования для настройки


Перед началом настройки убедитесь, что выполнены следующие предварительные требования.

- Подписка Azure.
- Последняя версия Azure PowerShell.
- Виртуальная сеть должна соответствовать следующим требованиям.
	- Набор префиксов IP-адресов для использования в виртуальных сетях в Azure
	- Набор локальных префиксов IP-адресов (может содержать общедоступные IP-адреса)
	- Шлюз виртуальной сети должен быть создан с подсетью /28.
	- Дополнительный набор префиксов IP-адресов (/28), находящихся за пределами виртуальной сети. Это будет использоваться для настройки пиринга BGP.
	- AS-номер для вашей сети. Дополнительные сведения об AS-номерах см. в статье [Номера автономных систем (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Хэш MD5, если требуется сеанс BGP с проверкой подлинности.
	- Идентификаторы виртуальных ЛС, по которым будет отправляться трафик. Для каждого канала потребуется по 2 идентификатора виртуальных ЛС: один для виртуальных сетей, а другой для служб, размещенных в общедоступных IP-адресах.
	- [Номера автономной системы (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) для вашей сети.
	- Два перекрестных подключения 1 Гбит/с / 10 Гбит/с к серверу Exchange Ethernet поставщика Exchange.
	- Пара маршрутизаторов, поддерживающих протокол BGP для маршрутизации

##  Рабочий процесс развертывания


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Настройка параметров с помощью PowerShell

Windows PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения см. в документации по PowerShell в [библиотеке MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Импортируйте модуль PowerShell для ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Получите список поддерживаемых поставщиков, расположений и значений пропускной способности.**

	Перед созданием канала потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности для каждого расположения. Следующий командлет PowerShell возвращает эти сведения, которые будет использоваться в последующих шагах.

    	PS C:> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths
		----                 -------------------------      --------------------------
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		                     as,New York,Seattle,Silicon
		                     Valley,Washington
		                     DC,London,Hong
		                     Kong,Singapore,Sydney,Tokyo
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		Exchange
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		IPVPN
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		International
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000

3. **Создание выделенного канала**

	В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте их данные при выполнении запроса.

	Ниже приведен пример запроса нового ключа службы.

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	Ответ будет примерно таким, как показано в следующем примере.

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Вы можете получить эти сведения в любое время с помощью командлета Get-AzureCircuit. Если выполнить этот вызов без параметров, то будут перечислены все каналы. Ваш ключ службы будет приведен в поле ServiceKey.

		PS C:> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Отправьте ключ службы своему поставщику Exchange.**

	Поставщик Exchange будет использовать этот ключ службы для включения своей стороны соединения. Вы должны будете предоставить поставщику услуг подключения дополнительные сведения для включения соединения.

5. **Периодически проверяйте состояние и статус ключа канала.**

	Это позволит узнать, когда поставщик подключит ваш канал. После включения канала состояние *ServiceProviderProvisioningState* будет отображаться как *Provisioned* (подготовлен), как показано в следующем примере.

		PS C:> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **Настройте маршрутизацию для виртуальной сети.**

	Мы используем сеансы BGP для обмена маршрутами, а также для проверки, что обеспечивается высокий уровень доступности. С помощью следующего примера создайте сеанс BGP для своего канала. Подставьте собственные значения при создании своего сеанса.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	Сведения о маршрутизации для канала можно получить с помощью командлета Get-AzureBGPPeering, предоставив ключ службы. Вы также можете обновить настройки BGP с помощью командлета Set-AzureBGPPeering. Сеанс BGP не будет устанавливаться во время выполнения этой команды. Для установки сеанса BGP канал должен быть связан по крайней мере с одной виртуальной сетью.

	В ответе, приведенном ниже, предоставлены сведения, необходимые для выполнения следующих действий. Используйте узел ASN для настройки BGP в VRF вашего маршрутизатора.

		PS C:> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Настройте маршрутизацию для служб, размещенных в общедоступных IP-адресах.**

	Мы используем сеансы BGP для обмена маршрутами, а также для проверки, что обеспечивается высокий уровень доступности. С помощью следующего примера создайте сеанс BGP для своего канала. Подставьте собственные значения при создании своего сеанса.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	Сведения о маршрутизации для канала можно получить с помощью командлета Get-AzureBGPPeering, предоставив ключ службы. Вы также можете обновить настройки BGP с помощью командлета Set-AzureBGPPeering. Сеанс BGP не будет устанавливаться во время выполнения этой команды. Для установки сеанса BGP канал должен быть связан по крайней мере с одной виртуальной сетью.

	В ответе, приведенном ниже, предоставлены сведения, необходимые для выполнения следующих действий. Используйте узел ASN для настройки BGP в VRF вашего маршрутизатора.

		PS C:> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **Настройте виртуальную сеть и шлюз.**

	См. раздел [Настройка виртуальной сети и шлюза для ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx) Обратите внимание, что подсеть шлюза должна быть /28 для работы с подключением ExpressRoute.

9. **Привяжите вашу сеть к каналу.** Выполняйте следующие инструкции только после получения подтверждения, что ваш канал имеет следующее состояние и статус:
	- ServiceProviderProvisioningState: Provisioned
	- Status: Enabled

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 

<!---HONumber=July15_HO2-->
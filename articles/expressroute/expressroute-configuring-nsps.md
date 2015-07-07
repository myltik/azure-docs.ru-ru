<properties 
   pageTitle="Настройка Expressroute через поставщиков сетевых услуг"
   description="В этом учебнике описан пошаговый процесс настройки ExpressRoute через поставщиков сетевых услуг (NSP)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Настройка подключения ExpressRoute через поставщика сетевых услуг

Чтобы настроить подключение ExpressRoute через поставщика сетевых услуг, необходимо выполнить несколько шагов в соответствующем порядке. Эти инструкции помогут вам сделать следующее.

- Создать каналы ExpressRoute и управлять ими
- Связывание виртуальной сети с каналом ExpressRoute

##  Предварительные требования для настройки


Перед началом настройки убедитесь, что выполнены следующие предварительные требования.

- Подписка Azure.
- Последняя версия Windows PowerShell
- Виртуальная сеть должна соответствовать следующим требованиям.
	- Набор префиксов IP-адресов для использования в виртуальных сетях в Azure.
	- Набор локальных префиксов IP-адресов (может содержать общедоступные IP-адреса).
	- Шлюз виртуальной сети должен быть создан с подсетью /28.
	- Дополнительный набор префиксов IP-адресов (/28), находящихся за пределами виртуальной сети. Будет использоваться для настройки маршрутов. — Необходимо предоставить поставщику сетевых услуг.
	- AS-номер для вашей сети. Необходимо будет предоставить поставщику сетевых услуг. Можно использовать частный AS-номер. При этом необходимо использовать номера > 65000. Дополнительную информацию об AS-номерах см. в разделе о номерах автономных систем. 

- От поставщика сетевых услуг необходимо получить следующие сведения.
	- У вас должна быть служба VPN, которую поддерживает ExpressRoute. Узнайте у поставщика сетевых услуг, соответствует ли этим требованиям существующая служба VPN.

##  Рабочий процесс развертывания

![Рабочий процесс поставщика сетевых услуг](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Настройка параметров с помощью PowerShell
Windows PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения см. в документации по PowerShell в [библиотеке MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).



1. **Импортируйте модуль PowerShell для ExpressRoute.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Получите список поддерживаемых поставщиков, расположений и значений пропускной способности.**

	Перед созданием канала потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности для каждого расположения. Следующий командлет PowerShell возвращает эти сведения, которые будет использоваться в последующих шагах.

		PS C:> Get-AzureDedicatedCircuitServiceProvider

	Возвращаемые сведения будут выглядеть приблизительно как в приведенном ниже примере:

		PS C:> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **Отправьте запрос на ключ службы и передайте его поставщику Exchange.**

	Для выполнения данного запроса используется командлет PowerShell. В этом примере мы используем AT&T Netbond в качестве поставщика услуг и укажем канал ExpressRoute в 50 Мбит/с в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте их данные при выполнении запроса.

	Ниже приведен пример запроса нового ключа службы.

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	Ответ будет примерно таким, как показано в следующем примере.

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Вы можете получить эти сведения в любое время с помощью командлета Get-AzureCircuit. Если выполнить этот вызов без параметров, то будут перечислены все каналы. Ваш ключ службы будет приведен в поле ServiceKey.

		PS C:> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	После завершения этого шага вы получите подключение к хранилищу Azure и другим службам.



4. **Настройте виртуальную сеть и шлюз.**

	См. раздел [Настройка виртуальной сети и шлюза для ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx) Обратите внимание, что подсеть шлюза должна быть /28 для работы с подключением ExpressRoute.

5. **Привяжите вашу сеть к каналу.**

	Выполните следующие действия, только убедившись в следующем:
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	Убедитесь, что у вас есть по крайней мере одна виртуальная сеть Azure с созданным шлюзом. Шлюз должен быть запущен.

		PS C:> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=62-->
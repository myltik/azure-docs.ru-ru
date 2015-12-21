<properties
   pageTitle="Процедура настройки канала ExpressRoute с помощью PowerShell | Microsoft Azure"
   description="В этой статье описана процедура создания и подготовки канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить и отозвать канал."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/08/2015"
   ms.author="cherylmc"/>

# Создание и изменение канала ExpressRoute с помощью PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

В этой статье описано, как создать канал ExpressRoute, используя командлеты PowerShell и классическую модель развертывания. Ниже описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Предварительные требования для настройки

- Вам потребуется последняя версия модулей Azure PowerShell. Последнюю версию модуля PowerShell можно загрузить из раздела PowerShell на [странице загрузок Azure](http://azure.microsoft.com/downloads). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell приведены в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). 
- Не забудьте изучить страницы [Предварительные требования](expressroute-prerequisites.md) и [Рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и подготовка канала ExpressRoute

1. **Импортируйте модуль PowerShell для ExpressRoute.**

 	Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Получите список поддерживаемых поставщиков, расположений и значений пропускной способности.**

	Перед созданием канала ExpressRoute потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности. Командлет PowerShell *Get-AzureDedicatedCircuitServiceProvider* возвращает сведения, которые будут использоваться в последующих шагах.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Создайте канал ExpressRoute.**

	В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте их данные при выполнении запроса.

	Ниже приведен пример запроса нового ключа службы.

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	Если же вы хотите создать канал ExpressRoute с надстройкой Premium, воспользуйтесь приведенным ниже примером. Дополнительные сведения о надстройке Premium см. на странице [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

		get-help new-azurededicatedcircuit -detailed 

4. **Получите список всех каналов ExpressRoute.**

	Для получения списка всех созданных каналов ExpressRoute можно использовать команду *Get-AzureDedicatedCircuit*.

		#Getting service key
		Get-AzureDedicatedCircuit

	Ответ будет примерно таким, как показано в следующем примере.

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Вы можете получить эти сведения в любое время с помощью командлета *Get-AzureDedicatedCircuit*. Если выполнить этот вызов без параметров, то будут перечислены все каналы. Ваш ключ службы будет приведен в поле *ServiceKey*.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Подробное описание всех параметров можно получить, выполнив следующую команду:

		get-help get-azurededicatedcircuit -detailed 

5. **Отправьте ключ службы поставщику подключения для инициализации.**

	При создании нового канала ExpressRoute он будет иметь следующее состояние:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	Параметр *ServiceProviderProvisioningState* сообщает сведения о текущем состоянии подготовки на стороне поставщика службы, а параметр Status — состояние на стороне корпорации Майкрософт. Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Периодически проверяйте состояние и статус ключа канала.**

	Это позволяет узнать, когда поставщик включил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Подготовлен*, как показано в следующем примере.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Создайте конфигурацию маршрутизации.**
	
	Пошаговые инструкции см. на странице [Конфигурация маршрутизации канала ExpressRoute (создание и изменение пиринга каналов)](expressroute-howto-routing-classic.md).

7. **Свяжите виртуальную сеть с каналом ExpressRoute.**

	Теперь свяжите виртуальную сеть с каналом ExpressRoute. Пошаговые инструкции см. на странице [Связывание каналов ExpressRoute с виртуальными сетями](expressroute-howto-linkvnet-classic.md). Инструкции по созданию виртуальной сети для ExpressRoute см. в статье [Создание виртуальной сети для ExpressRoute](expressroute-howto-createvnet-classic.md).

##  Получение состояния канала ExpressRoute

Вы можете получить эти сведения в любое время с помощью командлета *Get-AzureCircuit*. Если выполнить этот вызов без параметров, то будут перечислены все каналы.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Для получения сведений об определенном канале ExpressRoute передайте ключ службы как параметр вызова.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


Подробное описание всех параметров можно получить, выполнив следующую команду:

		get-help get-azurededicatedcircuit -detailed 

##  Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Можно сделать следующее:

- Включить или выключить надстройку Premium для канала ExpressRoute без простоев.
- Увеличить пропускную способность канала ExpressRoute без простоев.

Более подробные сведения об ограничениях см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md).

### Включение надстройки ExpressRoute Premium

Вы можете включить надстройку ExpressRoute Premium для существующего канала ExpressRoute с помощью следующего командлета PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

Теперь для вашего канала включена надстройка ExpressRoute Premium. Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку Premium.

### Выключение надстройки ExpressRoute Premium

Вы можете выключить надстройку ExpressRoute Premium для существующего канала с помощью следующего командлета PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Теперь надстройка Premium для вашего канала выключена.

>[AZURE.IMPORTANT]Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для стандартного канала.
>
>- Прежде чем переходить с надстройки Premium на Standard, убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.
- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.
- Для частного пиринга таблица маршрутизации должна содержать меньше 4000 маршрутов. Если в таблице маршрутизации больше 4000 маршрутов, сеанс BGP будет сброшен и снова активирован только после того, как количество объявленных префиксов станет меньше 4000.


### Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала. Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Размер вашего канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Обратите внимание, что с этого момента мы начнем тарифицировать обновленный параметр пропускной способности.

>[AZURE.IMPORTANT]Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.

##  Удаление и отзыв канала ExpressRoute

Для удаления канала ExpressRoute выполните следующую команду:

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Обратите внимание, что для успешного выполнения этой операции необходимо разорвать связи между ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "включено" на *отключено*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *не подготовлено*) до выполнения указанного выше командлета, мы отзовем канал и перестанем выставлять вам счета.

## Дальнейшие действия

- [Настройка маршрутизации](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_1210_2015-->
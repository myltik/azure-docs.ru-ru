<properties
   pageTitle="Создание и изменение канала ExpressRoute с помощью классической модели развертывания и Microsoft Azure PowerShell"
   description="В этой статье описана процедура создания и подготовки канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить и отозвать канал."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="ganesr"/>

# Создание и изменение канала ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

В этой статье описано, как создать канал ExpressRoute, используя командлеты PowerShell и классическую модель развертывания. В этой статье описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Перед началом работы

- Вам потребуется последняя версия модулей Azure PowerShell. Последнюю версию модуля PowerShell можно загрузить из раздела PowerShell на [странице загрузок Azure](https://azure.microsoft.com/downloads/). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell приведены в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Не забудьте изучить страницы [Предварительные требования](expressroute-prerequisites.md) и [Рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и предоставление канала ExpressRoute

### 1\. Импорт модуля PowerShell для ExpressRoute

 Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды.

	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### 2\. Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности.

Перед созданием канала ExpressRoute потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности.

Командлет PowerShell `Get-AzureDedicatedCircuitServiceProvider` возвращает эти сведения, которые будут использоваться в последующих шагах.

	Get-AzureDedicatedCircuitServiceProvider

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие данные (они потребуются при создании канала):

- Имя

- PeeringLocations

- BandwidthsOffered

Теперь вы готовы создать канал ExpressRoute.

### 3\. Создание канала ExpressRoute

В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте их данные при выполнении запроса.

>[AZURE.IMPORTANT] Выставление счетов за использование ExpressRoute начинается после того, как клиент получает служебный ключ. Выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.


Ниже приведен пример запроса нового ключа службы.

	$Bandwidth = 200
	$CircuitName = "MyTestCircuit"
	$ServiceProvider = "Equinix"
	$Location = "Silicon Valley"

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

Если же вы хотите создать канал ExpressRoute с надстройкой Premium, воспользуйтесь приведенным ниже примером. Дополнительные сведения о надстройке Premium см. на странице [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

	get-help new-azurededicatedcircuit -detailed 

### 4\. Получение списка всех каналов ExpressRoute

Для получения списка всех созданных каналов ExpressRoute можно использовать команду *Get-AzureDedicatedCircuit*.

		
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

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureDedicatedCircuit`. Если выполнить этот вызов без параметров, то будут перечислены все каналы. Ваш ключ службы будет приведен в поле *ServiceKey*.

	Get-AzureDedicatedCircuit

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

### 5\. Отправка ключа службы поставщику услуг подключения для подготовки


Параметр *ServiceProviderProvisioningState* предоставляет сведения о текущем состоянии подготовки на стороне поставщика службы, а параметр Status — состояние на стороне корпорации Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

При создании нового канала ExpressRoute он будет иметь следующее состояние:
	
	ServiceProviderProvisioningState : NotProvisioned	
	Status                           : Enabled


Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled


### 6\. Периодическая проверка состояния и статуса ключа канала

Это позволяет узнать, когда поставщик включил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Подготовлен*, как показано в следующем примере.

	Get-AzureDedicatedCircuit

	Bandwidth                        : 200
	CircuitName                      : MyTestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled

### 7\. Создание конфигурации маршрутизации
	
Пошаговые инструкции см. на странице [Конфигурация маршрутизации канала ExpressRoute (создание и изменение пиринга каналов)](expressroute-howto-routing-classic.md).

>[AZURE.IMPORTANT] Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно IPVPN, типа MPLS), ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.

### 8\. Связывание виртуальной сети с каналом ExpressRoute

Теперь свяжите виртуальную сеть с каналом ExpressRoute. Пошаговые инструкции см. на странице [Связывание каналов ExpressRoute с виртуальными сетями](expressroute-howto-linkvnet-classic.md). Инструкции по созданию виртуальной сети для ExpressRoute с помощью классической модели развертывания см. в статье [Настройка виртуальной сети для ExpressRoute](expressroute-howto-vnet-portal-classic.md).



##  Получение состояния канала ExpressRoute

Вы можете получить эти сведения в любое время с помощью командлета *Get-AzureCircuit*. Если выполнить этот вызов без параметров, то будут перечислены все каналы.

	Get-AzureDedicatedCircuit

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

	Get-AzureDedicatedCircuit -ServiceKey "*********************************"

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

Вы можете выполнять следующие действия без простоя:

- включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
- увеличивать пропускную способность канала ExpressRoute. Обратите внимание, что снижение пропускной способности канала не поддерживается. 
- Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Обратите внимание, что переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается. 
-  Параметр активации классических операций (Allow Classic Operations) можно включать и отключать. 

Более подробные сведения об ограничениях см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md).

### Включение надстройки ExpressRoute "Премиум"

Вы можете включить надстройку ExpressRoute Premium для существующего канала ExpressRoute с помощью следующего командлета PowerShell:
	
	Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Premium
	Status                           : Enabled

Теперь для вашего канала включена надстройка ExpressRoute Premium. Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку Premium.

### Отключение надстройки ExpressRoute "Премиум"

>[AZURE.IMPORTANT] Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для стандартного канала.

Обратите внимание на следующее:

- Прежде чем переходить с надстройки Premium на Standard, убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.

- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.

- Для частного пиринга таблица маршрутизации должна содержать меньше 4000 маршрутов. Если в таблице маршрутизации больше 4000 маршрутов, сеанс BGP будет сброшен и снова активирован только после того, как количество объявленных префиксов станет меньше 4000.

Вы можете выключить надстройку ExpressRoute Premium для существующего канала с помощью следующего командлета PowerShell:
	
	Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled



### Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала.

>[AZURE.IMPORTANT] Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.

Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

	Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
	Bandwidth                        : 1000
	CircuitName                      : TestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled

Размер вашего канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Обратите внимание, что с этого момента мы начнем тарифицировать обновленный параметр пропускной способности.



## Удаление и отзыв канала ExpressRoute

Обратите внимание на следующее:

- Для успешного выполнения этой операции необходимо разорвать связи между ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

- Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "включено" на *отключено*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

- Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *не подготовлено*) до выполнения указанного выше командлета, мы отзовем канал и перестанем выставлять вам счета.

Для удаления канала ExpressRoute выполните следующую команду:

	Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	



## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-classic.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0427_2016-->
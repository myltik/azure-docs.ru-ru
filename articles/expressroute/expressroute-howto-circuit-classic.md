<properties
   pageTitle="Создание и изменение канала ExpressRoute с помощью классической модели развертывания и PowerShell | Microsoft Azure"
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

В этой статье пошагово описано, как создать канал Azure ExpressRoute, используя командлеты PowerShell и классическую модель развертывания. В этой статье описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Перед началом работы

- Нужна последняя версия модулей Azure PowerShell. Последнюю версию модулей PowerShell можно скачать из раздела PowerShell на [странице скачивания Azure](https://azure.microsoft.com/downloads/). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и предоставление канала ExpressRoute

### 1\. Импорт модулей PowerShell для ExpressRoute

 Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните следующие команды.

	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### 2\. Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности

Перед созданием канала ExpressRoute потребуется список поддерживаемых поставщиков услуг подключения, расположений и вариантов пропускной способности.

Командлет PowerShell `Get-AzureDedicatedCircuitServiceProvider` возвращает эти сведения, которые будут использоваться в последующих шагах.

	Get-AzureDedicatedCircuitServiceProvider

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие данные, так как они потребуются позже, при создании канала.

- Имя

- PeeringLocations

- BandwidthsOffered

Теперь все готово к созданию канала ExpressRoute.

### 3\. Создание канала ExpressRoute

В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другой поставщик и другие параметры, подставьте в запрос соответствующие данные.

>[AZURE.IMPORTANT] Выставление счетов за использование ExpressRoute начинается после того, как клиент получает служебный ключ. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.


Пример запроса нового ключа службы:

	$Bandwidth = 200
	$CircuitName = "MyTestCircuit"
	$ServiceProvider = "Equinix"
	$Location = "Silicon Valley"

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Если же вы хотите создать канал ExpressRoute с надстройкой "Премиум", воспользуйтесь приведенным ниже примером. Дополнительные сведения о надстройке "Премиум" см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

	get-help new-azurededicatedcircuit -detailed

### 4\. Вывод списка всех каналов ExpressRoute

Чтобы получить список всех созданных вами каналов ExpressRoute, можно выполнить команду `Get-AzureDedicatedCircuit`.


	Get-AzureDedicatedCircuit

Ответ будет выглядеть примерно так:

	Bandwidth                        : 200
	CircuitName                      : MyTestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : NotProvisioned
	Sku                              : Standard
	Status                           : Enabled

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureDedicatedCircuit`. Если этот командлет вызывается без параметров, выводится список всех каналов. Ваш ключ службы будет приведен в поле *ServiceKey*.

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


Параметр *ServiceProviderProvisioningState* предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр *Status* предоставляет состояние на стороне Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет иметь следующее состояние:

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


Когда поставщик услуг подключения находится в процессе его включения, канал переходит в следующее состояние.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled


### 6\. Периодическая проверка состояния и статуса ключа канала

Это позволяет узнать, когда поставщик включил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Provisioned*, как показано в следующем примере.

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

Пошаговые инструкции см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-classic.md).

>[AZURE.IMPORTANT] Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно это IPVPN, например MPLS), то ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.

### 8\. Связывание виртуальной сети с каналом ExpressRoute

Теперь свяжите виртуальную сеть с каналом ExpressRoute. Пошаговые инструкции см. в статье [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md). Инструкции по созданию виртуальной сети для ExpressRoute с помощью классической модели развертывания см. в статье [Настройка виртуальной сети для ExpressRoute на классическом портале](expressroute-howto-vnet-portal-classic.md).

## Получение состояния канала ExpressRoute

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureCircuit`. Если этот командлет вызывается без параметров, выводится список всех каналов.

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

## Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие действия без простоя:

- включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
- увеличивать пропускную способность канала ExpressRoute. Обратите внимание, что снижение пропускной способности канала не поддерживается.
- Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Обратите внимание, что переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
- Параметр *Allow Classic Operations* (Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### Включение надстройки ExpressRoute "Премиум"

Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала ExpressRoute с помощью следующего командлета PowerShell.

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

>[AZURE.IMPORTANT] Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для канала "Стандартный".

Обратите внимание на следующее.

- Прежде чем переходить с надстройки Premium на Standard, убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам "Премиум".

- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам "Премиум".

- Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если в ней больше 4000 маршрутов, сеанс BGP будет удален. Его можно будет снова включить только после того, как количество объявленных префиксов станет меньше 4000.

Вы можете выключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell.

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

Варианты пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md). Можно выбрать любой размер, превышающий размер существующего канала.

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

Обратите внимание на следующее.

- Для успешного выполнения этой операции необходимо разорвать связи между каналом ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

- Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "Enabled" на *Disabling*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

- Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *Не подготовлено*) до выполнения указанного выше командлета, мы отзовем канал и перестанем выставлять вам счета.

Для удаления канала ExpressRoute выполните следующую команду:

	Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-classic.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0518_2016-->
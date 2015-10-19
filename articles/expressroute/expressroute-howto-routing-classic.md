<properties
   pageTitle="Настройка маршрутизации для канала ExpressRoute | Microsoft Azure"
   description="В этой статье описана процедура создания и подготовки частного пиринга, общедоступного пиринга и пиринга Microsoft для канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить пиринги для канала."
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
   ms.date="10/02/2015"
   ms.author="cherylmc"/>

# Создание и изменение конфигурации маршрутизации ExpressRoute

В этой статье описано, как создавать настройку маршрутизации ExpressRoute и управлять этой настройкой, используя командлеты PowerShell и классическую модель развертывания. Ниже описывается, как проверить состояние, обновить или удалить и отозвать пиринги для канала ExpressRoute.

>[AZURE.IMPORTANT]Обратите внимание, что в настоящее время Azure поддерживает две модели развертывания: классическую и с диспетчером ресурсов. Перед началом настройки внимательно изучите модели и средства развертывания. Дополнительную информацию о моделях развертывания см. в статье [Модели развертывания Azure](../azure-classic-rm.md).


## Предварительные требования для настройки

- Вам потребуется последняя версия модулей Azure PowerShell. Последнюю версию модуля PowerShell можно загрузить из раздела PowerShell на [странице загрузок Azure](http://azure.microsoft.com/downloads). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell приведены в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). 
- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Вам потребуется активный канал ExpressRoute. Перед тем как продолжить, [создайте канал ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Для выполнения описанных ниже командлетов канал ExpressRoute должен быть подготовлен и включен.

>[AZURE.IMPORTANT]Эти инструкции применяются только к каналам от поставщиков, предлагающих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно IPVPN, типа MPLS), ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно. В этом случае создавать пиринги и управлять ими вы не сможете.

Для каждого канала ExpressRoute можно настроить один, два или три пиринга (частный пиринг Azure, общедоступный пиринг Azure и пиринг Microsoft). Пиринги можно настраивать в любом порядке, главное, выполнять их конфигурацию по очереди.

## Частный пиринг Azure

В этом разделе описано, как создать, получить, обновить и удалить конфигурацию частного пиринга Azure для канала ExpressRoute.

### Создание частного пиринга Azure

1. **Импортируйте модуль PowerShell для ExpressRoute.**
	
	Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Создайте канал ExpressRoute.**
	
	Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.

3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**

	Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled


4. **Настройте для канала частный пиринг Azure.**

	Прежде чем продолжить, убедитесь в наличии следующих элементов:

	- Подсеть /30 для основной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
	- Подсеть /30 для дополнительной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
	- Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
	- Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Для этого пиринга можно использовать частный номер AS. Не используйте номер 65515.
	- MD5-хэш, если вы решите его использовать. **Это необязательно.**
	
	Чтобы настроить частный пиринг Azure для своего канала, выполните следующий командлет:

		New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

	Если вы решили использовать MD5-хэш, используйте следующий командлет:

		New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

	>[AZURE.IMPORTANT]Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.

### Получение сведений о частном пиринге Azure

Для получения сведений о конфигурации можно использовать следующий командлет:

	Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 10.0.0.0/30
	RoutingRegistryName            : 
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 10.0.0.4/30
	State                          : Enabled
	VlanId                         : 100


### Обновление конфигурации частного пиринга Azure

С помощью указанного ниже командлета можно обновить любую часть конфигурации. В приведенном ниже примере идентификатор виртуальной локальной сети канала можно обновить со 100 до 500.

	Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### Удаление частного пиринга Azure

Для удаления конфигурации пиринга выполните следующий командлет:

	Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

>[AZURE.IMPORTANT]Перед выполнением этого командлета отсоедините от канала ExpressRoute все виртуальные сети.

## Общедоступный пиринг Azure

В этом разделе описано, как создать, получить, обновить и удалить конфигурацию общедоступного пиринга Azure для канала ExpressRoute.

### Создание общедоступного пиринга Azure

1. **Импортируйте модуль PowerShell для ExpressRoute.**
	
	Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Создание канала ExpressRoute**
	
	Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.

3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**

	Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

	

4. **Настройте для канала общедоступный пиринг Azure.**

	Прежде чем продолжить, убедитесь, что у вас есть следующие сведения.

	- Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
	- Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
	- Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
	- Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Для этого пиринга необходимо использовать общедоступный номер AS.
	- MD5-хэш, если вы решите его использовать. **Это необязательно.**
	
	Чтобы настроить общедоступный пиринг Azure для своего канала, выполните следующий командлет:

		New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

	Если вы решили использовать MD5-хэш, используйте следующий командлет:

		New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

	>[AZURE.IMPORTANT]Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.

### Получение сведений об общедоступном пиринге Azure

Для получения сведений о конфигурации можно использовать следующий командлет:

	Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 131.107.0.0/30
	RoutingRegistryName            : 
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 131.107.0.4/30
	State                          : Enabled
	VlanId                         : 200


### Обновление конфигурации общедоступного пиринга Azure

С помощью указанного ниже командлета можно обновить любую часть конфигурации.

	Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

В приведенном выше примере идентификатор виртуальной локальной сети канала обновлен с 200 до 600.

### Удаление общедоступного пиринга Azure

Для удаления конфигурации пиринга выполните следующий командлет:

	Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## Пиринг Майкрософт

В этом разделе описано, как создать, получить, обновить и удалить конфигурацию пиринга Microsoft для канала ExpressRoute.

### Создание пиринга Майкрософт

1. **Импортируйте модуль PowerShell для ExpressRoute.**
	
	Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Создание канала ExpressRoute**
	
	Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.

3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**

	Сначала убедитесь, что канал ExpressRoute подготовлен и включен.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.

		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled


4. **Настройка пиринга Microsoft для канала**

	Перед началом работы убедитесь, что у вас есть следующие сведения.

	- Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
	- Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
	- Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
	- Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Используйте только общедоступные номера AS. Номер AS должен принадлежать вам.
	- Объявленные префиксы: необходимо предоставить список всех префиксов, которые вы планируете объявить во время сеанса BGP. Допускаются только общедоступные префиксы IP-адресов. Если планируется отправить набор префиксов, можно отправить список значений, разделенных запятыми. Эти префиксы должны быть зарегистрированы в RIR/IRR на ваше имя.
	- Клиент ASN: для объявления префиксов, не зарегистрированных с номером AS для пиринга, можно указать номер AS, с которым они зарегистрированы. **Это необязательно.**
	- Имя реестра маршрутизации: можно указать RIR/IRR, в котором зарегистрированы номер AS и префиксы.
	- MD5-хэш, если вы решите его использовать. **Это необязательно.**
	
	Чтобы настроить пиринг Microsoft для своего канала, выполните следующий командлет:

		New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### Получение сведений о пиринге Microsoft

Для получения сведений о конфигурации можно использовать следующий командлет:

	Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
	
	AdvertisedPublicPrefixes       : 123.0.0.0/30
	AdvertisedPublicPrefixesState  : Configured
	AzureAsn                       : 12076
	CustomerAutonomousSystemNumber : 2245
	PeerAsn                        : 1234
	PrimaryAzurePort               : 
	PrimaryPeerSubnet              : 10.0.0.0/30
	RoutingRegistryName            : ARIN
	SecondaryAzurePort             : 
	SecondaryPeerSubnet            : 10.0.0.4/30
	State                          : Enabled
	VlanId                         : 300


### Обновление конфигурации пиринга Microsoft

С помощью указанного ниже командлета можно обновить любую часть конфигурации.

		Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### Удаление пиринга Майкрософт

Для удаления конфигурации пиринга выполните следующий командлет:

	Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## Дальнейшие действия

-  Затем необходимо выполнить [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md).
-  Дополнительные сведения о рабочих процессах см. в разделе [Рабочие процессы ExpressRoute](expressroute-workflows.md).
-  Дополнительные сведения о пиринге канала см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).

<!---HONumber=Oct15_HO2-->
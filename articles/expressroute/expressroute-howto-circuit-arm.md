<properties
   pageTitle="Создание и изменение канала ExpressRoute с помощью диспетчера ресурсов и PowerShell | Microsoft Azure"
   description="В этой статье описывается создание и подготовка канала ExpressRoute, а также рассказывается, как проверить состояние канала, обновить его или удалить и отозвать."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="cherylmc"/>

# Создание и изменение канала ExpressRoute с помощью диспетчера ресурсов и PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

В этой статье объясняется, как создать канал Azure ExpressRoute, используя командлеты Windows PowerShell и модель развертывания диспетчера ресурсов Azure. Описанные ниже действия показывают, как проверить состояние канала, обновить его или удалить и отозвать.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Предварительные требования для настройки

Для создания канала ExpressRoute:

- Установите последнюю версию модулей Azure PowerShell — 1.0 или более позднюю. Пошаговые инструкции по настройке компьютера для использования модулей PowerShell см. на странице [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- Изучите страницы [Предварительные требования](expressroute-prerequisites.md) и [Рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и предоставление канала ExpressRoute

**Шаг 1. Импортируйте модуль PowerShell для ExpressRoute.**

Чтобы начать использовать командлеты ExpressRoute, установите последнюю версию установщика PowerShell из [коллекции PowerShell](http://www.powershellgallery.com/) и импортируйте модули диспетчера ресурсов в сеанс PowerShell. Запустите PowerShell от имени администратора.

```
Install-Module AzureRM

Install-AzureRM
```

Импортируйте все модули AzureRM в рамках диапазона известных семантических версий:

```
Import-AzureRM
```

Можно также импортировать только выбранный модуль в рамках диапазона известных семантических версий:

```
Import-Module AzureRM.Network
```

Войдите в свою учетную запись:

```
Login-AzureRmAccount
```

Выберите подписку, необходимую для создания канала ExpressRoute:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Шаг 2. Получите список поддерживаемых поставщиков, расположений и значений пропускной способности.**

Перед созданием канала ExpressRoute потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности. Командлет PowerShell *Get-AzureRmExpressRouteServiceProvider* возвращает сведения, которые будут использоваться в последующих шагах.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие данные (они потребуются при создании канала):

- Имя
- PeeringLocations
- BandwidthsOffered

Теперь вы готовы создать канал ExpressRoute.

**Шаг 3. Создайте канал ExpressRoute.**

Перед созданием канала ExpressRoute необходимо создать группу ресурсов (если вы этого еще не сделали) с помощью следующей команды:

```
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте в запрос соответствующие данные. Пример запроса нового ключа службы:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Убедитесь, что указаны правильный уровень SKU и семейство SKU:

- Уровень SKU определяет, какая надстройка включена — ExpressRoute Standard или ExpressRoute Premium. Укажите *Стандарт*, чтобы получить стандартную надстройку SKU, или *Премиум*, чтобы получить надстройку категории "Премиум".
- Семейство SKU определяет тип выставления счетов. Выберите *metereddata* для тарифного плана с оплатой за трафик или *unlimiteddata* для безлимитного тарифного плана. **Примечание.** После создания канала изменить тип выставления счетов нельзя.

Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Шаг 4. Получите список всех каналов ExpressRoute.**

Для приобретения списка всех созданных вами каналов ExpressRoute выполните команду *Get-AzureRmExpressRouteCircuit*.

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Вы получите ответ следующего вида:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
   		                           }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Эти данные можно узнать в любое время с помощью командлета *Get-AzureRmExpressRouteCircuit*. Если командлет вызывается без параметров, перечисляются все каналы. Ваш ключ службы будет приведен в поле *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

Вы получите ответ следующего вида:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
   		                           }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Подробное описание всех параметров можно получить, выполнив следующую команду:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Шаг 5. Отправьте ключ службы поставщику подключения для инициализации.**

Вновь созданный канал ExpressRoute будет иметь следующее состояние:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

Параметр *ServiceProviderProvisioningState* предоставляет сведения о текущем состоянии подготовки на стороне поставщика службы, а параметр Status — состояние на стороне корпорации Майкрософт. Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии:

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Шаг 6. Периодически проверяйте состояние и статус ключа канала.**

Проверка состояния и статуса ключа канала позволит вам узнать, когда поставщик активирует ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Подготовлен*, как показано в следующем примере:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Вы получите ответ следующего вида:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   	                            }
ServiceKey                       : **************************************
Peerings                         : []

```

**Шаг 7. Создайте конфигурацию маршрутизации.**

Пошаговые инструкции см. в статье [Создание и изменение маршрутизации канала ExpressRoute с помощью диспетчера ресурсов Azure и PowerShell](expressroute-howto-routing-arm.md).

**Шаг 8. Свяжите виртуальную сеть с каналом ExpressRoute.**

Теперь свяжите виртуальную сеть с каналом ExpressRoute. Если вы работаете в режиме развертывания Resource Manager, можно использовать [этот шаблон](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection). В настоящее время мы разрабатываем способ выполнения этого действия с помощью PowerShell.

## Получение состояния канала ExpressRoute

Эту информацию можно получить в любое время с помощью командлета *Get-AzureRmExpressRouteCircuit*. Если командлет вызывается без параметров, перечисляются все каналы.

```
Get-AzureRmExpressRouteCircuit
```

Ответ будет выглядеть примерно так:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
   		                             "ServiceProviderName": "Equinix",
   		                             "PeeringLocation": "Silicon Valley",
   		                             "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Для получения сведений об определенном канале ExpressRoute передайте имя группы ресурсов и имя канала как параметр вызова.

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Вы получите ответ следующего вида:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
   		                             "Tier": "Standard",
   		                             "Family": "MeteredData"
   		                           }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Подробное описание всех параметров можно получить, выполнив следующую команду:

```
get-help get-azurededicatedcircuit -detailed
```

## Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие действия без простоя:

- включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
- увеличивать пропускную способность канала ExpressRoute.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### Включение надстройки ExpressRoute "Премиум"

Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего фрагмента кода PowerShell:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Теперь для канала включены функции надстройки ExpressRoute "Премиум". Обратите внимание, что вам будет выставлен счет за использование возможностей надстройки "Премиум" с момента успешного выполнения команды.

### Отключение надстройки ExpressRoute "Премиум"

Вы можете отключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Теперь надстройка "Премиум" для канала отключена.

Обратите внимание, что эта операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для стандартного канала.

- Прежде чем переходить с канала "Премиум" на "Стандартный", убедитесь, что к каналу привязано менее 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и корпорация Майкрософт будет выставлять вам счета по тарифам ценовой категории "Премиум".
- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и корпорация Майкрософт будет выставлять вам счета по тарифам ценовой категории "Премиум".
- Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если в ней больше 4000 маршрутов, сеанс BGP будет сброшен. Его можно будет снова активировать только после того, как количество объявленных префиксов станет меньше 4000.

### Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала. Выберите необходимый размер и используйте следующую команду для изменения размера канала:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Размер канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Когда вы сообщите поставщику услуг об изменении размера канала, корпорация Майкрософт начнет выставлять счета за обновленную пропускную способность.

**Важно!** Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.

## Удаление и отзыв канала ExpressRoute

Для удаления канала ExpressRoute выполните следующую команду:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

Обратите внимание, что для успешного выполнения этой операции необходимо разорвать связи между каналом ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "Включено" на *Отключение*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Корпорация Майкрософт будет резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменится на *Не подготовлено*) до выполнения указанного выше командлета, корпорация Майкрософт отзовет канал и перестанет выставлять вам счета.

## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!----HONumber=AcomDC_0309_2016-->
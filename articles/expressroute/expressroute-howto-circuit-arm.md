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
   ms.date="12/04/2015"
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
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Обратите внимание, что для успешного выполнения этой операции необходимо разорвать связи между каналом ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "включено" на *отключено*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Корпорация Майкрософт будет резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *не подготовлено*) до выполнения указанного выше командлета, корпорация Майкрософт отзовет канал и перестанет выставлять вам счета.

## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->
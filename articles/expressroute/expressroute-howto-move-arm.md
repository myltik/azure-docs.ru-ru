<properties
   pageTitle="Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager | Microsoft Azure"
   description="В этой статье описывается перемещение классического канала в модель развертывания Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="ganesr"/>


# Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager

## Предварительные требования для настройки

- Вам потребуется последняя версия модулей Azure PowerShell (минимум 1.0).
- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Перед продолжением просмотрите сведения в разделе [Перемещение канала ExpressRoute из классической модели развертывания в модель Resource Manager](expressroute-move.md). Убедитесь, что вам полностью понятны пределы и ограничения доступных функций.
- Чтобы переместить канал Azure ExpressRoute из классической модели развертывания в модель развертывания с помощью Azure Resource Manager, канал в классической модели развертывания должен быть полностью настроен и работоспособен.
- Убедитесь в наличии группы ресурсов, созданной в модели развертывания Resource Manager.

## Перемещение канала ExpressRoute в модель развертывания Resource Manager

Канал ExpressRoute необходимо переместить в модель развертывания Resource Manager, чтобы его можно было использовать в классической модели и в модели Resource Manager. Для этого можно выполнить следующие команды PowerShell:

### Шаг 1. Соберите сведения о канале из классической модели развертывания

Сначала необходимо собрать сведения о канале ExpressRoute.

Войдите в классическую среду Azure и получите ключ службы. Для сбора информации можно использовать следующий фрагмент кода PowerShell:

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

Скопируйте **ключ службы** канала, который требуется перенести в модель развертывания Resource Manager.

### Шаг 2. Вход в среду Resource Manager и создание новой группы ресурсов

Чтобы создать группу ресурсов, можно выполнить следующий фрагмент кода:

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Также можно использовать существующую группу ресурсов, если она имеется.

### Шаг 3. Перемещение канала ExpressRoute в модель развертывания Resource Manager

Теперь все готово для перемещения канала ExpressRoute из классической модели развертывания в модель Resource Manager. Просмотрите сведения в разделе [Перемещение канала ExpressRoute из классической модели развертывания в модель Resource Manager](expressroute-move.md), прежде чем продолжать работу.

Для этого можно выполнить следующий фрагмент кода:

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] После завершения перемещения новое имя, указанное в предыдущем командлете, будет использоваться для адресации ресурсов. По существу канал будет переименован.

## Включение канала ExpressRoute для обеих моделей развертывания

Прежде чем вы сможете управлять доступом к модели развертывания, необходимо переместить канал ExpressRoute в модель развертывания Resource Manager.

Выполните следующий командлет, чтобы разрешить доступ к обеим моделям развертывания:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

После успешного завершения этой операции канал будет отображаться в классической модели развертывания.

Выполните следующий код, чтобы получить сведения о канале ExpressRoute:

    get-azurededicatedcircuit

Выходные данные должны содержать ключ службы. Теперь вы можете управлять связями с каналом ExpressRoute с помощью стандартных команд классического развертывания для классических виртуальных сетей и стандартных команд ARM для виртуальных сетей ARM. Следующие статьи содержат сведения об управлении ссылками на канал ExpressRoute:

- [Связывание виртуальных сетей с каналами ExpressRoute в модели развертывания диспетчера ресурсов](expressroute-howto-linkvnet-arm.md)
- [Связывание виртуальных сетей с каналами ExpressRoute в классической модели развертывания](expressroute-howto-linkvnet-classic.md)


## Отключение канала ExpressRoute в классической модели развертывания

Выполните следующий командлет, чтобы отключить доступ к классической модели развертывания:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

После успешного завершения этой операции канал не будет отображаться в классической модели развертывания.

## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0720_2016-->
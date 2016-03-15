<properties
   pageTitle="Настройка подключения через VPN-шлюз между виртуальными сетями с помощью Azure Resource Manager и PowerShell | Microsoft Azure"
   description="Эта статья описывает этапы настройки подключения между виртуальными сетями с помощью диспетчера ресурсов Azure и PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="cherylmc"/>

# Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell

> [AZURE.SELECTOR]
- [Классический портал Azure](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell и Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Эта статья содержит инструкции по созданию подключения между виртуальными сетями с помощью модели развертывания **Resource Manager** и PowerShell. Виртуальные сети могут располагаться в одном или разных регионах; также они могут использоваться в рамках одной или разных подписок.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## О подключениях "виртуальная сеть — виртуальная сеть"

Подключение одной виртуальной сети к другой (подключение типа VNet-to-VNet) очень похоже на подключение виртуальной сети к локальному сайту. В обоих типах подключений используется VPN-шлюз Azure для создания защищенного туннеля, использующего IPsec/IKE. Подключаемые виртуальные сети могут находиться в разных регионах или использоваться в рамках разных подписок. Можно даже комбинировать подключение виртуальных сетей с многосайтовыми конфигурациями. Это позволяет устанавливать сетевые топологии, совмещающие подключения между организациями с подключениями между виртуальными сетями, как показано на схеме ниже.


![Сведения о подключениях](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Что может дать связь между виртуальными сетями

Вам может потребоваться подключить виртуальные сети по следующим причинам.

- **Межрегиональная географическая избыточность и географическое присутствие**
	- Вы можете настроить собственную георепликацию или синхронизацию с безопасной связью без прохождения трафика через конечные точки с выходом в Интернет.
	- С помощью диспетчера трафика и балансировщика нагрузки Azure можно настроить высокодоступную рабочую нагрузку с геоизбыточностью в нескольких регионах Azure. Одним из важных примеров является настройка SQL Always On с группами доступности, распределенными между несколькими регионами Azure.

- **Региональные многоуровневые приложения с изоляцией или административной границей**
	- В одном регионе можно настроить многоуровневые приложения с несколькими виртуальными сетями, которые связанны друг с другом из-за требований к изоляции или административных требований.


### Часто задаваемые вопросы о подключениях VNet-VNet

- Виртуальные сети могут быть в одном или разных регионах (расположениях) Azure.

- Облачная служба или конечная точка с балансировкой нагрузки НЕ МОЖЕТ распространяться на виртуальные сети, даже если они соединены друг с другом.

- Для подключения нескольких виртуальных сетей Azure друг к другу не требуются локальные VPN-шлюзы, если только не требуется распределенное подключение.

- Подключения между виртуальными сетями поддерживают подключение нескольких виртуальных сетей. Не поддерживается подключение виртуальных машин или облачных служб, размещенных НЕ в виртуальной сети.

- Для подключения между виртуальными сетями необходимы VPN-шлюзы Azure с VPN типа RouteBased (этот тип раньше назывался динамической маршрутизацией).

- Подключение к виртуальной сети можно использовать одновременно с многосайтовыми сетями VPN, использующими до 10 (при использовании шлюзов по умолчанию или стандартных шлюзов) или до 30 туннелей VPN (при использовании высокопроизводительных шлюзов) для подключения VPN-шлюза виртуальной сети к другим виртуальным сетям или локальным сайтам.

- Адресные пространства виртуальных сетей и местных сайтов локальных сетей не должны перекрываться. Перекрытие адресных пространств приведет к сбою при создании подключения между виртуальными сетями.

- Избыточные туннели между парой виртуальных сетей не поддерживаются.

- Все туннели VPN виртуальной сети совместно используют доступную пропускную способность VPN-шлюза Azure и одно соглашение об уровне обслуживания VPN-шлюзов в Azure.

- Трафик между виртуальными сетями проходит через Microsoft Network, а не Интернет.

- Трафик между виртуальными сетями в одном регионе предоставляется бесплатно в обоих направлениях. Исходящий трафик между виртуальными сетями оплачивается с учетом внешней скорости передачи данных между виртуальными сетями в зависимости от исходных регионов. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## Каким инструкциям следовать?

В этой статье приводятся различные инструкции, которые применяются к виртуальным сетям, созданным с помощью модели развертывания Resource Manager. Действия по настройке подключения между виртуальными сетями зависят от того, используются ли сети в рамках одной или разных подписок.

![Оба подключения](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Основные различия в конфигурации зависят от того, можно ли создать и настроить все ресурсы шлюза и виртуальной сети в ходе одного сеанса PowerShell. Этот документ содержит инструкции для каждого варианта. На рисунке выше показаны два варианта подключения между виртуальными сетями: в рамках одной подписки и в рамках разных подписок.


- [Виртуальные сети в рамках одной подписки](#samesub)
- [Виртуальные сети в рамках разных подписках](#difsub)


## <a name ="samesub"/>Подключение между сетями в рамках одной подписки

Эта конфигурация применяется к виртуальным сетям, которые используются в рамках одной подписки, как показано на рисунке ниже.

![Подключение между сетями в рамках одной подписки](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Перед началом работы

- Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
	
- Вам потребуется установить командлеты PowerShell диспетчера ресурсов Azure. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

### <a name ="Step1"/>Шаг 1. Планирование диапазонов IP-адресов


Важно определить диапазоны адресов, которые будут использоваться при настройке сети. Имейте в виду, необходимо убедиться в том, что ни один из диапазонов виртуальных сетей или диапазонов локальных сетей никак не перекрываются.

Далее мы создадим две виртуальные сети с соответствующими шлюзами подсетей и конфигурациями. Затем мы создадим подключение VPN-шлюза между двумя виртуальными сетями.

В этом упражнении используйте следующие параметры виртуальных сетей.

**Значения для TestVNet1**:

- Имя виртуальной сети: TestVNet1
- Группа ресурсов: TestRG1
- Расположение: восточная часть США
- TestVNet1: 10.11.0.0/16 и 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS-сервер: 8.8.8.8
- Имя шлюза: VNet1GW
- Общедоступный IP-адрес: VNet1GWIP
- Тип VPN: RouteBased
- Подключение (1–4): VNet1toVNet4
- Подключение (1–5): VNet1toVNet5
- Тип подключения: VNet2VNet


**Значения для TestVNet4**:

- Имя виртуальной сети: TestVNet4
- TestVNet2: 10.41.0.0/16 и 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- Группа ресурсов: TestRG4
- Расположение: Запад США
- DNS-сервер: 8.8.8.8
- Имя шлюза: VNet4GW
- Общедоступный IP-адрес: VNet4GWIP
- Тип VPN: RouteBased
- Подключение: VNet4toVNet1
- Тип подключения: VNet2VNet



### <a name ="Step2"/>Шаг 2. Создание и настройка TestVNet1

1. Объявление переменных

	В этом упражнении мы начнем с объявления переменных. В примере ниже объявлены переменные со значениями для этого упражнения. Обязательно замените значения своими при настройке для рабочей среды. Эти переменные можно использовать для ознакомления с этим типом конфигурации. Измените переменные, а затем скопируйте и вставьте код в консоль PowerShell.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Подключение к подписке 1

	Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительную информацию см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

	Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

		Login-AzureRmAccount

	Просмотрите подписки учетной записи.

		Get-AzureRmSubscription 

	Укажите подписку, которую нужно использовать.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Создание новой группы ресурсов

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Создание конфигураций подсети для TestVNet1

	В примере ниже создается виртуальная сеть с именем TestVNet1 и три подсети: GatewaySubnet, FrontEnd и Backend. При замене значений важно, чтобы вы назвали подсеть шлюза именем GatewaySubnet. Если вы используете другое имя, шлюз не будет создан.

	В приведенном ниже примере используется подсеть шлюза /27. Хотя технически вы можете создать подсеть шлюза с помощью такой небольшой подсети, как /29, мы не рекомендуем это делать. Рекомендуется использовать подсети побольше, например /27 или /26, если вы захотите воспользоваться преимуществами существующих или будущих конфигураций, для которых может потребоваться большая подсеть шлюза.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Создание TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Запрос общедоступного IP-адреса

	Затем вы должны запросить общедоступный IP-адрес, который нужно выделить для шлюза, создаваемого для виртуальной сети. Указать необходимый IP-адрес нельзя; он выделяется для шлюза динамически. Этот IP-адрес будет использоваться на следующем этапе конфигурации. Используйте пример ниже. Для этого адреса нужно использовать метод динамического распределения (Dynamic).

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Создание конфигурации шлюза

	Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Создание шлюза для TestVNet1

	На этом шаге вы создадите шлюз для виртуальной сети TestVNet1. Для подключений типа VNet-to-VNet необходимо использовать тип VPN RouteBased. Создание шлюза может занять некоторое время (30 минут или более).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Шаг 3. Создание и настройка TestVNet4

После настройки TestVNet1 вы повторите шаги для создания TestVNet4. Следуйте инструкциям ниже, заменив при необходимости значения своими. Этот шаг можно выполнять в том же сеансе PowerShell, так как работа выполняется в одной и той же подписке.

1. Объявление переменных

	Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Прежде чем продолжить, убедитесь, что вы все еще подключены к подписке 1.

2. Создание новой группы ресурсов

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Создание конфигураций подсети для TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Создание TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Запрос общедоступного IP-адреса

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Создание конфигурации шлюза

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Создание шлюза TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Шаг 4. Подключение шлюзов

1. Получение обоих шлюзов для виртуальных сетей

	В нашем примере этот шаг выполняется в том же сеансе PowerShell, так как оба шлюза используются в рамках одной подписки.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Создание подключения между TestVNet1 и TestVNet4

	На этом шаге вы создадите подключение между TestVNet1 и TestVNet4. Вы увидите ссылки на общий ключ в примерах. Можно использовать собственные значения для общего ключа. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Создание подключения между сетями TestVNet4 и TestVNet1

	Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet4 в сеть TestVNet1. Убедитесь, что общие ключи совпадают.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Подключение должно установиться через несколько минут.

## <a name ="Verify"/>Проверка подключения между виртуальными сетями

В приведенных ниже примерах показано, как проверить подключение. Обязательно измените значения в соответствии с вашей средой.

### Проверка подключения с помощью портала Azure

VPN-подключение на портале Azure можно проверить, выбрав **Шлюзы виртуальной сети** -> **имя шлюза** -> **Параметры** -> **Подключения**. Выбрав имя подключения, можно просмотреть дополнительные сведения в колонке **Подключение**.


### Проверка подключения с помощью PowerShell

Подключение также можно проверить командлетом *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Вы можете использовать следующий пример, заменив значения своими. При появлении запроса выберите "A", чтобы запустить команду All (Все).

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

После завершения работы командлета просмотрите результаты, которые он выдал. В следующем примере выходных данных PowerShell показано, что подключение установлено (состояние *Connected*), а также указан объем полученных и отправленных данных в байтах.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>Подключение между сетями в рамках разных подписок

После выполнения шагов ниже будет добавлено дополнительное подключение между виртуальными сетями, которое подключит сеть TestVNet1 в сети TestVNet5, используемую в рамках другой подписки. Разница заключается в том, что часть настройки нужно выполнить в отдельном сеансе PowerShell в контексте второй подписки, особенно если две подписки используются разными организациями. После выполнения следующих шагов будет создана итоговая конфигурация, которая показана на схеме ниже.

![Подключение между сетями в рамках разных подписок](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Приведенные ниже инструкции продолжают действия, описанные выше. Необходимо выполнить [шаг 1](#Step1) и [шаг 2](#Step2) для создания и настройки сети TestVNet1 и VPN-шлюза для TestVNet1. Если вы планируете подключать только виртуальные сети в рамках разных подписок, шаги 3 и 4 из предыдущего упражнения можно пропустить и перейти сразу к шагу 5.

### Ша 5. Проверка дополнительных диапазонов IP-адресов

Важно проследить, чтобы пространство IP-адресов для новой виртуальной сети TestVNet5 не пересекалось с диапазонами других виртуальных сетей или диапазонами шлюзов локальной сети.

В этом примере виртуальные сети могут принадлежать разным организациям. В этом упражнении используйте следующие значения для сети TestVNet5.

**Значения для TestVNet5**:

- Имя виртуальной сети: TestVNet5
- Группа ресурсов: TestRG5
- Расположение: восточная часть Японии
- TestVNet5: 10.51.0.0/16 и 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS-сервер: 8.8.8.8
- Имя шлюза: VNet5GW
- Общедоступный IP-адрес: VNet5GWIP
- Тип VPN: RouteBased
- Подключение: VNet5toVNet1
- Тип подключения: VNet2VNet

**Дополнительные значения для TestVNet1**:

- Подключение: VNet1toVNet5


### Шаг 6. Создание и настройка TestVNet5

Это действие необходимо выполнить в контексте новой подписки. Эту часть может выполнить администратор в другой организации, которой принадлежит подписка.

1. Объявление переменных

	Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Подключение к подписке 5

	Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

		Login-AzureRmAccount

	Просмотрите подписки учетной записи.

		Get-AzureRmSubscription 

	Укажите подписку, которую нужно использовать.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Создание новой группы ресурсов

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Создание конфигураций подсети для TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Создание TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Запрос общедоступного IP-адреса

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Создание конфигурации шлюза

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig 

8. Создание шлюза TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Шаг 7. Подключение шлюзов

Так как шлюзы из нашего примера находятся в разных подписках, мы разделили этот шаг на два сеанса PowerShell, обозначенные как [подписка 1] и [подписка 5].

1. **[Подписка 1]** Получение шлюза виртуальной сети для подписки 1

	Не забудьте войти и подключиться к подписке 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 5 по электронной почте или другим способом.

		$vnet1gw.Name
		$vnet1gw.Id

	Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Подписка 5]** Получение шлюза виртуальной сети для подписки 5

	Не забудьте войти и подключиться к подписке 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 1 по электронной почте или другим способом.

		$vnet5gw.Name
		$vnet5gw.Id

	Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Подписка 1]** Создание подключения между TestVNet1 и TestVNet5

	На этом шаге вы создадите подключение между TestVNet1 и TestVNet5. Разница здесь заключается в том, что вы не можете напрямую получить $vnet5gw, так как это значение используется в другой подписке. Вам необходимо создать новый объект PowerShell с помощью значений, переданных из подписки 1 на предыдущих этапах. Замените имя, идентификатор и общий ключ своими значениями. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

	Не забудьте подключиться к подписке 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Подписка 5]** Создание подключения между TestVNet5 и TestVNet1

	Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet5 в сеть TestVNet1. На этом шаге применяется такой же процесс создания объекта PowerShell с использованием значений, полученных из подписки 1. Проследите на этом шаге, чтобы общие ключи совпадали.

	Не забудьте подключиться к подписке 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Проверка подключения

	После выполнения этих действий можно проверить подключение с помощью методов, описанных в разделе [Проверка подключения между виртуальными сетями](#Verify).

## Дальнейшие действия

Установив подключение, можно добавить виртуальные машины в виртуальные сети. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0309_2016-->
<properties
   pageTitle="Подключение виртуальных сетей Azure с помощью VPN-шлюза и PowerShell | Microsoft Azure"
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
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# Настройка подключения между виртуальными сетями в развертывании Resource Manager с помощью PowerShell

> [AZURE.SELECTOR]
- [Классический портал Azure](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell — Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Эта статья содержит инструкции по созданию подключения между виртуальными сетями в модели развертывания Resource Manager с помощью VPN-шлюза. Виртуальные сети могут относиться к одному или разным регионам и к одной или разным подпискам.


![Схема подключения между виртуальными сетями](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)


### Модели развертывания и инструменты для подключения между виртуальными сетями


[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Подключение "виртуальная сеть–виртуальная сеть" можно настроить в обеих моделях развертывания с помощью различных средств. Дополнительные сведения приведены в таблице ниже. Мы обновляем эту таблицу по мере выпуска новых статей, моделей развертывания и дополнительных инструментов для этой конфигурации. Если статья доступна, таблица ссылается на нее напрямую.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


#### Пиринговая связь между виртуальными сетями

Для создания подключения можно использовать пиринговую связь между виртуальными сетями, если конфигурация виртуальной сети соответствует определенным требованиям. При пиринговой связи между виртуальными сетями шлюз виртуальной сети не используется. [Пиринговая связь](../virtual-network/virtual-network-peering-overview.md) между виртуальными сетями сейчас доступна в виде предварительной версии.


## О подключениях "виртуальная сеть — виртуальная сеть"

Подключение типа "виртуальная сеть — виртуальная сеть" похоже на подключение виртуальной сети к локальному сайту. В обоих типах подключений используется VPN-шлюз Azure для создания защищенного туннеля, использующего IPsec/IKE. Подключаемые виртуальные сети могут находиться в разных регионах или использоваться в рамках разных подписок. Можно даже комбинировать подключение виртуальных сетей с многосайтовыми конфигурациями. Это позволяет устанавливать топологии сети, совмещающие распределенные подключения с подключениями между виртуальными сетями, как показано на схеме ниже.


![Сведения о подключениях](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Что может дать связь между виртуальными сетями

Вам может потребоваться подключить виртуальные сети по следующим причинам.

- **Межрегиональная географическая избыточность и географическое присутствие**
	- Вы можете настроить собственную георепликацию или синхронизацию с безопасной связью без прохождения трафика через конечные точки с выходом в Интернет.
	- С помощью Azure Load Balancer и диспетчера трафика можно настроить высокодоступную рабочую нагрузку с геоизбыточностью в нескольких регионах Azure. Одним из важных примеров является настройка SQL Always On с группами доступности, распределенными между несколькими регионами Azure.

- **Региональные многоуровневые приложения с изоляцией или административной границей**
	- В одном регионе можно настроить многоуровневые приложения с несколькими виртуальными сетями, которые связаны друг с другом из-за требований к изоляции или административных требований.


### Часто задаваемые вопросы о подключениях VNet-VNet

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]


## Каким инструкциям следовать?

В этой статье приведены два разных набора действий. Один предназначен для [виртуальных сетей в рамках одной подписки](#samesub), а другой — для [виртуальных сетей в разных подписках](#difsub). Основные различия между этими наборами действий зависят от того, можно ли создать и настроить все ресурсы шлюза и виртуальной сети в ходе одного сеанса PowerShell.

В описанных ниже действиях используются переменные, объявляемые в начале каждого раздела. Если вы уже работаете с существующими виртуальными сетями, измените переменные в соответствии с параметрами своей среды.

![Оба подключения](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>Подключение виртуальных сетей из одной подписки

![Схема подключения между виртуальными сетями](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### Перед началом работы
	
Сначала вам потребуется установить командлеты PowerShell Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

### <a name="Step1"></a>Шаг 1. Планирование диапазонов IP-адресов


Далее мы создадим две виртуальные сети с соответствующими шлюзами и конфигурациями подсетей. Затем мы создадим VPN-подключение между двумя виртуальными сетями. В конфигурации сети важно задать диапазоны IP-адресов. Имейте в виду, необходимо убедиться в том, что ни один из диапазонов виртуальных сетей или диапазонов локальных сетей никак не перекрываются.

В примерах мы используем следующие значения:

**Значения для TestVNet1:**

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


**Значения для TestVNet4:**

- Имя виртуальной сети: TestVNet4
- TestVNet2: 10.41.0.0/16 и 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Группа ресурсов: TestRG4
- Расположение: Запад США
- DNS-сервер: 8.8.8.8
- Имя шлюза: VNet4GW
- Общедоступный IP-адрес: VNet4GWIP
- Тип VPN: RouteBased
- Подключение: VNet4toVNet1
- Тип подключения: VNet2VNet



### <a name="Step2"></a>Шаг 2. Создание и настройка TestVNet1

1. Объявление переменных

	Сначала объявите переменные. В этом примере объявлены переменные со значениями для этого упражнения. В большинстве случаев эти значения вам нужно заменить собственными. Но вы можете использовать указанные переменные, чтобы ознакомиться с этим типом конфигурации. Измените переменные (при необходимости), а затем скопируйте и вставьте их в консоль PowerShell.

		$Sub1 = "Replace_With_Your_Subcription_Name"
		$RG1 = "TestRG1"
		$Location1 = "East US"
		$VNetName1 = "TestVNet1"
		$FESubName1 = "FrontEnd"
		$BESubName1 = "Backend"
		$GWSubName1 = "GatewaySubnet"
		$VNetPrefix11 = "10.11.0.0/16"
		$VNetPrefix12 = "10.12.0.0/16"
		$FESubPrefix1 = "10.11.0.0/24"
		$BESubPrefix1 = "10.12.0.0/24"
		$GWSubPrefix1 = "10.12.255.0/27"
		$DNS1 = "8.8.8.8"
		$GWName1 = "VNet1GW"
		$GWIPName1 = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14 = "VNet1toVNet4"
		$Connection15 = "VNet1toVNet5"

2. Подключение к подписке

	Для работы с командлетами Resource Manager перейдите в режим PowerShell. Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

		Login-AzureRmAccount

	Просмотрите подписки учетной записи.

		Get-AzureRmSubscription 

	Укажите подписку, которую нужно использовать.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Создание новой группы ресурсов

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Создание конфигураций подсети для TestVNet1

	В этом примере создается виртуальная сеть с именем TestVNet1 и три подсети: GatewaySubnet, FrontEnd и Backend. При замене значений важно, чтобы вы назвали подсеть шлюза именем GatewaySubnet. Если вы используете другое имя, шлюз не будет создан.

	В следующем примере используются переменные, заданные ранее. В этом примере в подсети шлюза используется длина префикса /27. Вы можете создать подсеть шлюза с небольшим значением длины префикса, например /29, но мы не рекомендуем это делать. Лучше использовать значения побольше, например /27 или /26. Это позволит воспользоваться преимуществами существующих или будущих конфигураций, для которых может потребоваться подсеть шлюза большего размера.

		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Создание TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
		-Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Запрос общедоступного IP-адреса

	Запросите выделение общедоступного IP-адреса для шлюза, который будет создан для виртуальной сети. Учтите, что параметр AllocationMethod является динамическим. Указать необходимый IP-адрес нельзя. Он выделяется для шлюза динамически.

		$gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
		-Location $Location1 -AllocationMethod Dynamic

7. Создание конфигурации шлюза

	Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

		$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
		-Subnet $subnet1 -PublicIpAddress $gwpip1

8. Создание шлюза для TestVNet1

	На этом шаге вы создадите шлюз для виртуальной сети TestVNet1. Для подключений типа VNet-to-VNet необходимо использовать тип VPN RouteBased. Создание шлюза может занять некоторое время (45 минут или более).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
		-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
		-VpnType RouteBased -GatewaySku Standard

### Шаг 3. Создание и настройка TestVNet4

После настройки TestVNet1 создайте TestVNet4. Следуйте инструкциям ниже, заменив при необходимости значения своими. Этот шаг можно выполнять в том же сеансе PowerShell, так как мы работаем в той же подписке.

1. Объявление переменных

	Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

		$RG4 = "TestRG4"
		$Location4 = "West US"
		$VnetName4 = "TestVNet4"
		$FESubName4 = "FrontEnd"
		$BESubName4 = "Backend"
		$GWSubName4 = "GatewaySubnet"
		$VnetPrefix41 = "10.41.0.0/16"
		$VnetPrefix42 = "10.42.0.0/16"
		$FESubPrefix4 = "10.41.0.0/24"
		$BESubPrefix4 = "10.42.0.0/24"
		$GWSubPrefix4 = "10.42.255.0/27"
		$DNS4 = "8.8.8.8"
		$GWName4 = "VNet4GW"
		$GWIPName4 = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41 = "VNet4toVNet1"

	Прежде чем продолжить, убедитесь, что вы все еще подключены к подписке 1.

2. Создание новой группы ресурсов

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Создание конфигураций подсети для TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Создание TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
		-Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Запрос общедоступного IP-адреса

		$gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
		-Location $Location4 -AllocationMethod Dynamic

6. Создание конфигурации шлюза

		$vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Создание шлюза TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
		-Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
		-VpnType RouteBased -GatewaySku Standard

### Шаг 4. Подключение шлюзов

1. Получение обоих шлюзов для виртуальных сетей

	В нашем примере этот шаг выполняется в том же сеансе PowerShell, так как оба шлюза используются в рамках одной подписки.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Создание подключения между TestVNet1 и TestVNet4

	На этом шаге вы создадите подключение между TestVNet1 и TestVNet4. Вы увидите ссылки на общий ключ в примерах. Можно использовать собственные значения для общего ключа. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
		-VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
		-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Создание подключения между сетями TestVNet4 и TestVNet1

	Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet4 в сеть TestVNet1. Убедитесь, что общие ключи совпадают.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
		-VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
		-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Подключение должно установиться через несколько минут.

4. Проверьте подключение. См. раздел о [проверке подключения](#verify).


## <a name="difsub"></a>Подключение виртуальных сетей из разных подписок


![Схема подключения между виртуальными сетями](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

В этом сценарии мы создадим подключение между TestVNet1 и TestVNet5. TestVNet1 и TestVNet5 находятся в разных подписках. В этой конфигурации добавляется дополнительное подключение между виртуальными сетями для подключения TestVNet1 к TestVNet5.

Разница заключается в том, что часть настройки нужно выполнить в отдельном сеансе PowerShell в контексте второй подписки, особенно если две подписки используются разными организациями.

Следующие инструкции продолжают действия, описанные выше. Чтобы создать и настроить сеть TestVNet1 и VPN-шлюз для нее, необходимо выполнить [шаг 1](#Step1) и [шаг 2](#Step2). Выполнив шаг 1 и шаг 2, продолжайте выполнение шага 5, чтобы создать сеть TestVNet5.

### Ша 5. Проверка дополнительных диапазонов IP-адресов

Важно проследить, чтобы пространство IP-адресов для новой виртуальной сети TestVNet5 не пересекалось с диапазонами других виртуальных сетей или диапазонами шлюзов локальной сети.

В этом примере виртуальные сети могут принадлежать разным организациям. В этом упражнении используйте следующие значения для сети TestVNet5.

**Значения для TestVNet5:**

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

**Дополнительные значения для TestVNet1:**

- Подключение: VNet1toVNet5


### Шаг 6. Создание и настройка TestVNet5

Это действие необходимо выполнить в контексте новой подписки. Эту часть может выполнить администратор в другой организации, которой принадлежит подписка.

1. Объявление переменных

	Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

		$Sub5 = "Replace_With_the_New_Subcription_Name"
		$RG5 = "TestRG5"
		$Location5 = "Japan East"
		$VnetName5 = "TestVNet5"
		$FESubName5 = "FrontEnd"
		$BESubName5 = "Backend"
		$GWSubName5 = "GatewaySubnet"
		$VnetPrefix51 = "10.51.0.0/16"
		$VnetPrefix52 = "10.52.0.0/16"
		$FESubPrefix5 = "10.51.0.0/24"
		$BESubPrefix5 = "10.52.0.0/24"
		$GWSubPrefix5 = "10.52.255.0/27"
		$DNS5 = "8.8.8.8"
		$GWName5 = "VNet5GW"
		$GWIPName5 = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51 = "VNet5toVNet1"

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

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
		-AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Запрос общедоступного IP-адреса

		$gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
		-Location $Location5 -AllocationMethod Dynamic


7. Создание конфигурации шлюза

		$vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Создание шлюза TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
		-IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Шаг 7. Подключение шлюзов

Так как шлюзы из нашего примера находятся в разных подписках, мы разделили этот шаг на два сеанса PowerShell, обозначенные как [подписка 1] и [подписка 5].

1. **[Подписка 1]**. Получение шлюза виртуальной сети для подписки 1

	Обязательно войдите и подключитесь к подписке 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 5 по электронной почте или другим способом.

		$vnet1gw.Name
		$vnet1gw.Id

	Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Подписка 5]**. Получение шлюза виртуальной сети для подписки 5

	Обязательно войдите и подключитесь к подписке 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 1 по электронной почте или другим способом.

		$vnet5gw.Name
		$vnet5gw.Id

	Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Подписка 1]**. Создание подключения между TestVNet1 и TestVNet5

	На этом шаге вы создадите подключение между TestVNet1 и TestVNet5. Разница здесь заключается в том, что вы не можете напрямую получить $vnet5gw, так как это значение используется в другой подписке. Вам необходимо создать новый объект PowerShell с помощью значений, переданных из подписки 1 на предыдущих этапах. Замените имя, идентификатор и общий ключ своими значениями. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

	Обязательно подключитесь к подписке 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Подписка 5]**. Создание подключения между TestVNet5 и TestVNet1

	Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet5 в сеть TestVNet1. На этом шаге процесс создания объекта PowerShell аналогичный, но с использованием значений, полученных из подписки 1. Проследите на этом шаге, чтобы общие ключи совпадали.

	Обязательно подключитесь к подписке 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Проверка подключения

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## Дальнейшие действия

- Установив подключение, можно добавить виртуальные машины в виртуальные сети. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0831_2016-->
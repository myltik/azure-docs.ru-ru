Для выполнения этой задачи используйте виртуальную сеть со значениями, приведенными ниже. В этом списке также приведены дополнительные параметры и имена. Мы не используем этот список непосредственно ни на каком из шагов, несмотря на то, что добавляем переменные согласно значениям в этом списке. Вы можете скопировать список для справки, заменив значения собственными.

Справочный список для настройки.

* Имя виртуальной сети: TestVNet.
* Адресное пространство виртуальной сети: 192.168.0.0/16.
* Группа ресурсов — TestRG.
* Имя подсети Subnet1: FrontEnd. 
* Адресное пространство Subnet1: 192.168.0.0/16.
* Имя подсети шлюза: GatewaySubnet; подсеть шлюза всегда необходимо называть *GatewaySubnet*.
* Адресное пространство шлюза подсети: 192.168.200.0/26.
* Расположение — East US.
* Имя шлюза: GW.
* IP-имя шлюза: GWIP.
* Имя конфигурации IP-адресов шлюза: gwipconf.
* Type = "ExpressRoute". Для настройки ExpressRoute требуется этот тип.
* Общедоступное IP-имя шлюза: gwpip.

## <a name="add-a-gateway"></a>Добавление шлюза
1. Подключитесь к своей подписке Azure. 
   
        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. Объявите переменные для этого упражнения. Будут использоваться переменные, приведенные в примере ниже. Не забудьте изменить его в соответствии с параметрами, которые вы хотите использовать. 
   
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"
3. Сохраните объект виртуальной сети как переменную.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
4. Создайте подсеть шлюза своей виртуальной сети. Подсеть шлюза должна иметь имя GatewaySubnet. Потребуется создать шлюз со значением /27 или больше (26, 25 и т. д.).
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
5. Теперь нужно настроить конфигурацию.
   
            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
6. Сохраните подсеть шлюза как переменную.
   
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
7. Запросите общедоступный IP-адрес. IP-адрес запрашивается перед созданием шлюза. Указать необходимый IP-адрес нельзя, он выделяется динамически. Этот IP-адрес будет использоваться на следующем этапе конфигурации. Параметр AllocationMethod должен иметь значение Dynamic.
   
        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
8. Создайте конфигурацию для шлюза. Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. На этом шаге вы задаете конфигурацию, которая будет использоваться при создании шлюза. Но пока объект шлюза не создается. Используйте следующий пример, чтобы создать конфигурацию шлюза. 
   
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
9. Создайте шлюз. На этом шаге особенно важен параметр **-GatewayType** . Необходимо использовать значение **ExpressRoute**. Обратите внимание, что после выполнения этих командлетов на создание шлюза может потребоваться 20 минут или больше.
   
        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Проверка создания шлюза
Используйте следующую команду, чтобы проверить, был ли создан шлюз.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Изменение размера шлюза
Существует несколько [номеров SKU шлюзов](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Чтобы изменить SKU шлюза в любое время, можно использовать следующую команду.

> [!IMPORTANT]
> Эта команда не работает для шлюза UltraPerformance. Чтобы заменить шлюз на UltraPerformance, сначала удалите существующий шлюз ExpressRoute, а затем создайте шлюз UltraPerformance. Чтобы заменить шлюз UltraPerformance обычным шлюзом, сначала удалите шлюз UltraPerformance, а затем создайте новый шлюз.
> 
> 

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Удаление шлюза
Используйте следующую команду, чтобы удалить шлюз.

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  


<!--HONumber=Oct16_HO2-->



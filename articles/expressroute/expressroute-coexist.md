<properties
   pageTitle="Настройка подключения существующих одновременно VPN типа ExpressRoute и «сеть-сеть» "
   description="В этом учебнике описывается настройка существующих одновременно подключений к сети VPN ExpressRoute и сети типа «сеть-сеть»."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="cherylmc"/>

# Настройка подключения существующих одновременно VPN типа ExpressRoute и «сеть-сеть»

Сети VPN типа ExpressRoute и «сеть-сеть» теперь можно подключать к одной и той же виртуальной сети. Существует два различных сценария и две процедуры настройки.

### Сценарии

Существует два различных сценария. На следующем рисунке показаны оба сценария.

- **Сценарий 1**. Если имеется одна локальная сеть, то сеть ExpressRoute будет активной, а VPN «сеть-сеть» будет в резерве. В случае сбоя подключения к сети ExpressRoute VPN-подключение «сеть-сеть» становится активным. Этот сценарий лучше всего подходит для случаев, когда нужно обеспечить высокий уровень доступности.

- **Сценарий 2**. Если имеется две локальные сети, то одна из них подключается к Azure с помощью ExpressRoute, а другая — через VPN «сеть-сеть». В этом случае оба соединения активны одновременно.


![Существуют одновременно](media/expressroute-coexist/coexist1.jpg)


### Процедуры настройки

Далее описаны обе процедуры настройки. Выбор процедуры настройки зависит от того, существует ли уже виртуальная сеть, к которой необходимо подключиться, или требуется создать новую виртуальную сеть.


- [Создание новой виртуальной сети с существующими подключениями](#create-a-new-vnet-with-coexisting-connections). Если у вас еще нет виртуальной сети, то, выполнив эту процедуру, вы сможете создать новую виртуальную сеть и подключить ее к сети VPN ExpressRoute и сети типа «сеть-сеть».  


- [Настройка существующей виртуальной сети для существующих подключений](#configure-your-existing-vnet-for-coexisting-connections). Возможно, у вас уже имеется виртуальная сеть, а также подключения к VPN типа «сеть-сеть» или к ExpressRoute. Для создания одновременно существующих подключений необходимо удалить имеющийся шлюз, а затем настроить новые шлюзы, которые могут существовать одновременно. Это означает, что у вас будет перерыв в работе подключений между организациями, на время удаления и повторного создания шлюза и подключений, но вам не потребуется выполнять миграцию всех виртуальных машин и служб в новую виртуальную сеть. Виртуальные машины и службы смогут по-прежнему осуществлять связь через балансировщик нагрузки во время настройки шлюза, если они настроены соответствующим образом.

	Выполнив эту процедуру, вы сможете удалить шлюз, а затем создать новое подключение к сети VPN ExpressRoute и сети типа «сеть-сеть». Обратите внимание, что при создании новых подключений, необходимо выполнять действия в строго определенном порядке. При создании шлюзов и подключений не пользуйтесь инструкциями, взятыми из других статей.

### Примечания и ограничения

- Оба шлюза, к сети VPN ExpressRoute и к сети типа «сеть-сеть», должны иметь номера SKU Standard или HighPerformance. Сведения о номерах SKU шлюзов см. в разделе [Номера SKU шлюзов](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Если локальная сеть подключена как к сети VPN ExpressRoute, так и к сети типа «сеть-сеть» (сценарий 1), необходимо иметь статический маршрут, определенный в локальной сети, для маршрутизации VPN-подключения типа «сеть-сеть» к Интернету. 
- Сначала необходимо создать шлюз ExpressRoute, а затем — добавить VPN-шлюз к сети типа «сеть-сеть».
- Нельзя настроить маршрутизацию (через Azure) от локальной сети, подключенной к сети VPN типа «сеть-сеть», до локальной сети, подключенной к сети ExpressRoute.
- В обеих процедурах предполагается, что канал ExpressRoute уже настроен. Если это не так, см. следующие статьи. 

	- [Настройка подключения ExpressRoute через поставщика сетевых услуг (NSP)](expressroute-configuring-nsps.md) 
	- [Настройка подключения ExpressRoute через поставщика Exchange (EXP)](expressroute-configuring-exps.md)


## Создание новой виртуальной сети с существующими подключениями

1. Убедитесь, что у вас установлена последняя версия командлетов PowerShell. Вы можете загрузить и установить последнюю версию командлетов PowerShell из раздела PowerShell [страницы «Загрузка»](http://azure.microsoft.com/downloads/).
2. Создайте схему для виртуальной сети. Дополнительные сведения о работе с файлом конфигурации сети см. в разделе [Настройка виртуальной сети с помощью файла конфигурации сети](../virtual-network/virtual-networks-using-network-configuration-file.md). Дополнительные сведения о схеме конфигурации см. в разделе [Схема конфигурации виртуальной сети Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	При создании схемы, убедитесь, что используются следующие значения.

	- Подсеть шлюза для виртуальной сети должна иметь значение /27 (или более короткий префикс).
	- Тип подключения шлюза — «выделенное».

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. После создания и настройки XML-файла схемы отправьте этот файл. Это будет созданием виртуальной сети.

	Чтобы отправить файл с измененными значениями, используйте следующий командлет.

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Создайте шлюз ExpressRoute. Не забудьте указать для номера SKU шлюза значение *Standard* или *HighPerformance*, а для типа шлюза — значение *DynamicRouting*. 

	Используйте пример, описанный ниже, подставив свои собственные значения.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Свяжите шлюз ExpressRoute с каналом ExpressRoute. После выполнения этого действия подключение локальной сети к Azure через ExpressRoute будет установлено.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. Далее создайте VPN-шлюз к сети типа «сеть-сеть». Номер SKU шлюза должен иметь значение *Standard* или *HighPerformance*, а тип шлюза должен быть *DynamicRouting*.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	Чтобы получить параметры шлюза виртуальной сети, включая идентификатор шлюза и общедоступный IP-адрес, используйте командлет `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway
		
		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Создайте сущность VPN-шлюза локального сайта. Эта команда не настраивает локальный VPN-шлюз. Она только позволяет указать параметры локального шлюза, такие как общедоступный IP-адрес и локальное адресное пространство, чтобы VPN-шлюз Azure мог подключиться к нему.

	**Важно!** Локальный сайт для сети VPN типа «сеть-сеть» не определяется в netcfg. Этот командлет можно использовать только для указания параметров локального сайта. Его нельзя определить с помощью портала управления или файла netcfg.

	Используйте пример, описанный ниже, подставив свои собственные значения.

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	Чтобы получить параметры шлюза виртуальной сети, включая идентификатор шлюза и общедоступный IP-адрес, используйте командлет `Get-AzureVirtualNetworkGateway`. См. пример ниже.

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. Настройте локальное VPN-устройство для подключения к новому шлюзу. При настройке VPN-устройства используйте сведения, полученные на этапе 6. Дополнительные сведения о настройке VPN-устройства см. в разделе [Настройка VPN-устройства](vpn-gateway-configure-vpn-gateway-mp.md/#gather-information-for-your-vpn-device-configuration).
	

9. Свяжите VPN-шлюз к сети типа «сеть-сеть» в Azure с локальным шлюзом.

	В этом примере connectedEntityId — идентификатор локального шлюза, который можно узнать, выполнив `Get-AzureLocalNetworkGateway`. virtualNetworkGatewayId можно узнать с помощью командлета `Get-AzureVirtualNetworkGateway`. После выполнения этого действия подключение локальной сети к Azure через сеть VPN типа «сеть-сеть» будет установлено.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## Настройка существующей виртуальной сети для существующих подключений

При наличии существующей виртуальной сети, подключенной через сеть VPN ExpressRoute или сеть типа «сеть-сеть», чтобы установить подключение к существующей виртуальной сети, необходимо сначала удалить существующий шлюз. Это означает, что локальные пользователи потеряют подключение к виртуальной сети через шлюз на время выполнения этой настройки.

**Перед началом настройки.** Убедитесь, что у вас достаточно свободных IP-адресов в виртуальной сети, и соответственно можно увеличить размер подсети шлюза.


1. Убедитесь, что у вас установлена последняя версия командлетов PowerShell. Вы можете загрузить и установить последнюю версию командлетов PowerShell из раздела PowerShell [страницы «Загрузка»](http://azure.microsoft.com/downloads/).
 
2. Удалите существующий VPN-шлюз к сети типа «сеть-сеть». Используйте командлет, приведенный ниже, подставив свои собственные значения.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Экспортируйте схему виртуальной сети. Используйте командлет PowerShell, приведенный ниже, подставив свои собственные значения.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. Измените схему файла конфигурации сети так, чтобы подсеть шлюза имела значение /27 (или более короткий префикс). См. пример ниже. Дополнительные сведения о работе с файлом конфигурации сети см. в разделе [Настройка виртуальной сети с помощью файла конфигурации сети](../virtual-network/virtual-networks-using-network-configuration-file.md). Дополнительные сведения о схеме конфигурации см. в разделе [Схема конфигурации виртуальной сети Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. Если ранее шлюз был к сети VPN типа «сеть-сеть», необходимо также изменить тип подключения на **выделенный**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. На этом этапе вы будете иметь виртуальную сеть без шлюзов. Вы можете выполнить **Этап 3**, описанный в данной статье, [Создание новой виртуальной сети с существующими подключениями](#create-a-new-vnet-with-coexisting-connections) для создания новых шлюзов и осуществления подключений.



## Дальнейшие действия
	
Дополнительные сведения об ExpressRoute. См. раздел [Обзор ExpressRoute](expressroute-introduction.md).

Дополнительные сведения о VPN-шлюзах. См. раздел [Сведения о VPN-шлюзах](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=July15_HO2-->
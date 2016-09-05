.<properties 
   pageTitle="Настройка времени ожидания простоя TCP для подсистемы балансировки нагрузки | Microsoft Azure"
   description="Время ожидания простоя TCP подсистемы балансировки нагрузки"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />

# Как изменить параметры времени ожидания простоя TCP для подсистемы балансировки нагрузки

В конфигурации по умолчанию значение параметра времени ожидания простоя для подсистемы балансировки нагрузки Azure — 4 минуты.

Это означает, что, если период бездействия сеанса TCP или HTTP превысил значение времени ожидания, наличие подключения между клиентом и службой не гарантируется.

При закрытии подключения клиентское приложение получит сообщение об ошибке, аналогичное следующему: «Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером».

Стандартный метод для поддержки подключения в активном состоянии в течение длительного периода бездействия — проверка активности TCP (см. примеры для .NET [здесь](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

При использовании этого метода в случае отсутствия активности подключения отправляются пакеты. Благодаря поддержке непрерывной сетевой активности значение времени ожидания простоя не достигается и подключение поддерживается в течение длительного периода.

Идея заключается в настройке выполнения проверки активности TCP с меньшим интервалом, чем значение времени ожидания по умолчанию, чтобы избежать закрытия подключения, или увеличении значения времени ожидания простоя для продолжения сеанса подключения TCP.

Проверка активности TCP — идеальный вариант для сценариев без ограничений, связанных с аккумуляторами. Однако этот метод, как правило, не подходит для мобильных приложений. Использование этого метода в мобильном приложении может привести к быстрой разрядке аккумулятора устройства.

Для поддержки таких сценариев мы добавили возможность настройки времени ожидания простоя. Теперь для него можно задать значение от 4 до 30 минут. Этот параметр действует только для входящих подключений.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Как изменить параметры времени ожидания простоя в виртуальных машинах и облачных службах

- Настройка времени ожидания TCP для конечной точки виртуальной машины с помощью PowerShell или API управления службами
- Настройка времени ожидания TCP для наборов конечных точек с балансировкой нагрузки с помощью PowerShell или API управления службами.
- Настройка времени ожидания TCP для общедоступного IP-адреса уровне экземпляра
- Настройка времени ожидания TCP для веб-ролей или рабочих ролей с помощью модели службы
 

>[AZURE.NOTE] Примите во внимание, что некоторые команды содержатся только в последней версии пакета Azure PowerShell. Если эта команда PowerShell не найдена, скачайте последнюю версию пакета PowerShell.

 
### Настройка для времени ожидания TCP для общедоступного IP-адреса уровня экземпляра значения 15 минут

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes — необязательный параметр. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты.

>[AZURE.NOTE] Допустимый диапазон времени ожидания — от 4 до 30 минут.
 
### Установка времени ожидания простоя при создании конечной точки Azure в виртуальной машине

Изменение параметра времени ожидания для конечной точки

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
Получение конфигурации времени ожидания простоя

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
 
### Установка времени ожидания TCP для набора балансировки нагрузки для конечных точек

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует установить время ожидания TCP.

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### Изменение параметров времени ожидания для облачных служб

Вы можете обновить облачную службу с помощью пакета SDK для Azure для .NET 2.4.

Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить время ожидания TCP для развертывания облачных служб, нужно обновить развертывание. Исключение применяется, если время ожидания TCP задано только для общедоступного IP-адреса. Параметры общедоступного IP-адреса находятся в CSCFG-файле, и их можно обновить в процессе обновления развертывания и обновления до следующей версии.

Ниже перечислены изменения в настройках конечной точки в CSCFG-файле:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

Изменения CSCFG-файла для параметров времени ожидания общедоступных IP-адресов:

	<NetworkConfiguration>
 	 <VirtualNetworkSite name="VNet"/>
 	 <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
 	 </AddressAssignments>
	</NetworkConfiguration>

## Пример для Rest API

Вы можете настроить время ожидания простоя TCP с помощью API управления службами. Обязательно добавьте заголовок x-ms-version с номером версии 01.06.2014 или более поздним.
 
Обновление конфигурации указанных конечных точек ввода с балансировкой нагрузки для всех виртуальных машин развертывания
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-get-started-internet-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

 

<!---HONumber=AcomDC_0824_2016-->
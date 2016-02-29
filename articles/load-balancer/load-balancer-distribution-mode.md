<properties 
   pageTitle="Настройка режима распределения подсистемы балансировки нагрузки | Microsoft Azure"
   description="Как настроить режим распределения подсистемы балансировки нагрузки Azure для поддержки соответствия исходному IP-адресу"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="joaoma" />


# Режим распределения для балансировщика нагрузки (сходство исходного IP-адреса)

Мы ввели новый режим распределения, который называется «соответствие исходному IP-адресу» (также известное как соответствие сеансу или соответствие клиентскому IP-адресу). С помощью этого режима для подсистемы балансировки нагрузки Azure можно настроить использование 2 кортежей (исходный IP-адрес, IP-адрес назначения) или 3 кортежей (исходный IP-адрес, IP-адрес назначения, протокол) для сопоставления трафика с доступными серверами. При использовании соответствия исходному IP-адресу подключения, инициированные с одного клиентского компьютера, направляются к одной конечной точке DIP.

![подсистема балансировки нагрузки на основе хэша](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Режим соответствия исходному IP-адресу позволяет устранить несовместимость подсистемы балансировки нагрузки Azure и шлюза удаленных рабочих столов. Теперь можно создать ферму шлюза удаленных рабочих столов в одной облачной службе. Другой сценарий использования — отправка мультимедиа, при которой фактическая передача данных осуществляется по протоколу UDP, а управление — по протоколу TCP.

- Сначала клиент инициирует сеанс TCP для общедоступного IP-адреса с балансировкой нагрузки. Этот запрос отправляется по конкретному DIP, а сам канал остается активным, чтобы контролировать состояние подключения.
- С того же клиентского компьютера для той же общедоступной конечной точки с балансировкой нагрузки инициируется новый сеанс UDP. Ожидается, что это подключение будет направлено на ту же конечную точку DIP, что и предыдущее TCP-подключение. Таким образом можно передавать мультимедиа с более высокой пропускной способностью, поддерживая при этом канал управления по TCP.
 
Обратите внимание, что при изменении набора балансировки нагрузки (при добавлении или удалении виртуальной машины) распределение запросов клиента вычисляется заново. Нельзя надеяться на то, что новые подключения из существующих клиентских сеансов будут направлены на один сервер. Кроме того, использование режима распределения соответствия исходному IP-адресу может привести к неравномерному распределению трафика. Клиенты, работающие на прокси-серверах, могут рассматриваться как одно уникальное клиентское приложение.

В основе алгоритма распределения лежит использование хэша 5 кортежей (исходный IP-адрес, порт источника, IP-адрес назначения, порт назначения, тип протокола) для сопоставления трафика с доступными серверами. Он позволяет закреплять IP-адреса только в рамках сеанса транспорта. Пакеты в одном сеансе TCP или UDP будут направляться на один экземпляр IP-адреса центра обработки данных (DIP) в конечной точке с балансировкой нагрузки. Когда клиент закрывает и снова открывает подключение или начинает новый сеанс с того же исходного IP-адреса, порт источника изменяется и перенаправляет трафик к другой конечной точке DIP.

![подсистема балансировки нагрузки на основе хэша](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## Настройка параметров соответствия исходному IP-адресу для подсистемы балансировки нагрузки
 
Для виртуальных машин можно использовать Powershell, чтобы изменить параметры времени ожидания:
 
Добавление конечной точки Azure в виртуальную машину и установка режима распределения балансировщика нагрузки

	Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

>[AZURE.NOTE] Для LoadBalancerDistribution можно задать sourceIP для балансировки нагрузки по 2 кортежам (исходный IP-адрес, IP-адрес назначения), sourceIPProtocol для балансировки нагрузки по 3 кортежам (исходный IP-адрес, IP-адрес назначения, протокол) или none для поведения по умолчанию (балансировка нагрузки по 5 кортежам).


Получение конфигурации режима распределения балансировщика нагрузки для конечной точки

	PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
	LoadBalancerDistribution : sourceIP
 
Если элемент LoadBalancerDistribution отсутствует, подсистема балансировки нагрузки Azure использует алгоритм балансировки нагрузки по умолчанию (балансировка нагрузки по 5 кортежам).

 
### Установка режима распределения для набора балансировки нагрузки для конечных точек

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует установить режим распределения:

	Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocol TCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### Настройка облачной службы для изменения режима распределения

Вы можете обновить облачную службу с помощью пакета SDK для Azure для .NET 2.5 (который выйдет в ноябре). Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить режим распределения балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание. Ниже приведен пример изменений в CSDEF-файле для настройки конечных точек:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
  	<Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
  	</Endpoints>
	</WorkerRole>
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


## Пример API

Вы можете настроить распределение балансировщика нагрузки с помощью API управления службами. Обязательно добавьте заголовок `x-ms-version` с номером версии `2014-09-01` или выше.
 
Обновление конфигурации указанного набора балансировки нагрузки в развертывании

Пример запроса

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 

	x-ms-version: 2014-09-01 

	Content-Type: application/xml 

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
	<InputEndpoint> 
	<LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
	<LocalPort> local-port-number </LocalPort> 
	<Port> external-port-number </Port> 
	<LoadBalancerProbe> 
	<Port> port-assigned-to-probe </Port> 
	<Protocol> probe-protocol </Protocol> 
	<IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
	<TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
	</LoadBalancerProbe> 
	<Protocol> endpoint-protocol </Protocol> 
	<EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
	<IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
	<LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
	</InputEndpoint> 
	</LoadBalancedEndpointList>

LoadBalancerDistribution может иметь значение sourceIP для соответствия по 2 кортежам, sourceIPProtocol для соответствия по 3 кортежам или none (для отсутствия соответствия, т. е. балансировки по 5 кортежам).

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-internet-getstarted.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->
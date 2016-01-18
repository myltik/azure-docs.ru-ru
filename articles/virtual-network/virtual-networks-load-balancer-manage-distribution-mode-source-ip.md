<properties
   pageTitle="Управление: режим распределения балансировщика нагрузки (соответствие исходному IP-адресу)"
   description="Функции управления для режима распределения балансировщика нагрузки Azure"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"
   />

# Управление виртуальной сетью: режим распределения балансировщика нагрузки (соответствие исходному IP-адресу)
**Соответствие исходному IP-адресу** (также используется термин **соответствие сеансу** или **соответствие клиентскому IP-адресу**) — специальный режим распределения балансировщика нагрузки Azure, который привязывает подключения одного клиента к одному серверу, размещенному в Azure, а не динамически распределяет клиентские подключения по разным серверам, размещенным в Azure (режим работы балансировщика нагрузки по умолчанию).

С помощью соответствия исходному IP-адресу для балансировщика нагрузки Azure можно настроить использование сочетания 2 кортежей (исходный IP-адрес, целевой IP-адрес) или 3 кортежей (исходный IP-адрес, целевой IP-адрес, протокол) для сопоставления трафика с пулом доступных серверов, размещенных в Azure. При использовании соответствия исходному IP-адресу подключения, инициированные с одного клиентского компьютера, обрабатываются одной конечной точкой DIP (одним сервером, размещенным в Azure).

## Происхождение службы

Режим соответствия исходному IP-адресу позволяет устранить существовавшую ранее [несовместимость балансировщика нагрузки Azure и шлюза удаленных рабочих столов (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Реализация

Соответствие исходному IP-адресу можно настроить для:

* [Конечные точки виртуальных машин](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Настройка комплекта балансировки нагрузки](../load-balancer/load-balancer-overview.md)
* [Веб-роли](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Рабочие роли](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Сценарии
1. Кластер шлюза удаленных рабочих столов с использованием одной облачной службы.
2. Отправка мультимедиа (т. е. UDP для данных и TCP для управления).
  * Клиент инициирует сеанс TCP для общедоступного IP-адреса с балансировкой нагрузки, размещенного в Azure.
  * Балансировщик нагрузки отправляет запрос клиента по DIP. Этот канал остается активным, чтобы контролировать состояние подключения.
  * Клиент инициирует сеанс UDP для того же общедоступного IP-адреса с балансировкой нагрузки, размещенного в Azure.
  * Балансировщик нагрузки Azure направляет запрос к той же конечной точке DIP, что и для подключения TCP.
  * Клиент передает мультимедиа по UDP с более высокой пропускной способностью, поддерживая при этом канал управления по TCP для обеспечения надежности.

## Предупреждения
* При изменении набора балансировки нагрузки (т. е. при добавлении или удалении виртуальной машины) распределение каналов клиента вычисляется заново. Новые подключения могут обрабатываться сервером, отличным от использованного изначально.
* Использование соответствия исходному IP-адресу может привести к неравномерному распределению трафика между серверами, размещенными в Azure.
* Клиенты, маршрутизирующие трафик через прокси-сервер, могут интерпретироваться балансировщиком нагрузки Azure как один клиент.

## Примеры для PowerShell
Рекомендуем скачать [последний выпуск Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases).

### Добавление конечной точки Azure в виртуальную машину и установка режима распределения балансировщика нагрузки

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM  

Для LoadBalancerDistribution можно задать sourceIP для балансировки нагрузки по 2 кортежам (исходный IP-адрес, целевой IP-адрес), sourceIPProtocol для балансировки нагрузки по 3 кортежам (исходный IP-адрес, целевой IP-адрес, протокол) или none для поведения по умолчанию (балансировка нагрузки по 5 кортежам).

### Получение конфигурации режима распределения балансировщика нагрузки для конечной точки
    PS C:\> Get-AzureVM –ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint

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

Если элемент LoadBalancerDistribution отсутствует, балансировщик нагрузки Azure использует алгоритм балансировки нагрузки по умолчанию (балансировка нагрузки по 5 кортежам).

### Установка режима распределения для набора балансировки нагрузки для конечных точек

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует установить режим распределения.

## Примеры с облачной службой

Вы можете обновить облачную службу с помощью пакета SDK для Azure для .NET.

Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить режим распределения балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание.

Ниже приведен пример изменений в CSDEF-файле для настройки конечных точек:

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

## Примеры с API

Разработчики могут настроить распределение балансировщика нагрузки с помощью API управления службами. Обязательно добавьте заголовок x-ms-version с номером версии 01.09.2014 или более поздним.

### Обновление конфигурации указанного набора балансировки нагрузки в развертывании

#### Запрос

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

#### Ответ

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

<!---HONumber=AcomDC_0107_2016-->
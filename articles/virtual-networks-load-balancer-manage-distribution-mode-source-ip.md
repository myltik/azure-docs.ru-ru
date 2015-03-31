<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Управление: Режим распределения балансировщика нагрузки (соответствие исходному IP-адресу)" 
   description="Функции управления для режима распределения балансировщика нагрузки Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="02/20/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
   />
   
# Управление виртуальной сетью Режим распределения балансировщика нагрузки (соответствие исходному IP-адресу)
=======
   /> 
   
# Управление виртуальной сетью Режим распределения балансировщика нагрузки (соответствие исходному IP-адресу)
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md

**Соответствие исходному IP-адресу** (также известное, как **соответствие сеансу** или **соответствие клиентскому IP-адресу**), являющееся режимом распределения балансировщика нагрузки Azure, привязывает подключения одного клиента к одному серверу, размещенному в Azure, а не динамически распределяет каждое клиентское подключение по разным серверам, размещенным в Azure (поведение балансировщика нагрузки по умолчанию).

С помощью соответствия исходному IP-адресу для балансировщика нагрузки Azure можно настроить использование сочетания 2 кортежей (исходный IP-адрес, целевой IP-адрес) или 3 кортежей (исходный IP-адрес, целевой IP-адрес, протокол) для сопоставления трафика с пулом доступных серверов, размещенных в Azure. При использовании соответствия исходному IP-адресу подключения, инициированные с одного клиентского компьютера, обрабатываются одной конечной точкой DIP (одним сервером, размещенным в Azure).

## Происхождение службы

Соответствие исходному IP-адресу позволяет решить предыдущую [несовместимость балансировщика нагрузки Azure и шлюза удаленных рабочих столов (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Реализация

Соответствие исходному IP-адресу можно настроить для: 

* [конечных точек виртуальных машин](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/);
* [наборов балансировки нагрузки для конечных точек](http://msdn.microsoft.com/library/azure/dn655055.aspx);
* [Веб-роли](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Рабочие роли](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Сценарии
1. Кластер шлюза удаленных рабочих столов с использованием одной облачной службы.
2. Отправка мультимедиа (т. е. UDP для данных и TCP для управления).
  * Клиент инициирует сеанс TCP для общедоступного IP-адреса с балансировкой нагрузки, размещенного в Azure.
  * Балансировщик нагрузки отправляет запрос клиента по DIP. Этот канал остается активным, чтобы контролировать состояние подключения.
  * Клиент инициирует сеанс UDP для того же общедоступного IP-адреса с балансировкой нагрузки, размещенного в Azure.
  * Балансировщик нагрузки Azure направляет запрос к той же конечной точке DIP, что и для подключения TCP.
  * Клиент передает мультимедиа по UDP с более высокой пропускной способностью, поддерживая при этом канал управления по TCP для обеспечения надежности.
  
## Предупреждения
* При изменении набора балансировки нагрузки (т. е. при добавлении или удалении виртуальной машины) распределение каналов клиента вычисляется заново. Новые подключения могут обрабатываться сервером, отличным от использованного изначально.
* Использование соответствия исходному IP-адресу может привести к неравномерному распределению трафика между серверами, размещенными в Azure.
* Клиенты, маршрутизирующие трафик через прокси-сервер, могут интерпретироваться балансировщиком нагрузки Azure как один клиент.

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
=======
## Дальнейшие действия
* ПОДЛЕЖИТ УТОЧНЕНИЮ
   
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
## Примеры для PowerShell
Скачайте [последний выпуск Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) для достижения лучших результатов.

### Добавление конечной точки Azure в виртуальную машину и установка режима распределения балансировщика нагрузки

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM  
=======
    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€"LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM  
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md

Для LoadBalancerDistribution можно задать sourceIP для балансировки нагрузки по 2 кортежам (исходный IP-адрес, целевой IP-адрес), sourceIPProtocol для балансировки нагрузки по 3 кортежам (исходный IP-адрес, целевой IP-адрес, протокол) или none для поведения по умолчанию (балансировка нагрузки по 5 кортежам).  

### Получение конфигурации режима распределения балансировщика нагрузки для конечной точки

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
=======
    PS C:\> Get-AzureVM â€"ServiceName â€œMyServiceâ€� â€"Name â€œMyVMâ€� | Get-AzureEndpoint
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
    
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

<<<<<<< HEAD:articles/virtual-networks-load-balancer-manage-distribution-mode.md
    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -LoadBalancerDistribution "sourceIP"
=======
    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€"LoadBalancerDistribution "sourceIP"
>>>>>>> 5781a6382194f50134d2a16bd9d72a6cca290f3d:articles/virtual-networks-load-balancer-manage-distribution-mode-source-ip.md
    
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

Разработчики могут настроить распределение балансировщика нагрузки с помощью API управления службами.  Обязательно добавьте заголовок x-ms-version, для которого задана версия 2014-09-01 или более поздняя версия.

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

LoadBalancerDistribution может иметь значение sourceIP для соответствия по 2 кортежам, sourceIPProtocol для соответствия по 3 кортежам или none (для отсутствия соответствия, т. е. балансировки по 5 кортежам).

#### Ответ

    HTTP/1.1 202 Accepted 
    Cache-Control: no-cache 
    Content-Length: 0 
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
    x-ms-servedbyregion: ussouth2 
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
    Date: Thu, 16 Oct 2014 22:49:21 GMT

<!--HONumber=47-->

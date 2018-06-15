---
title: Настройка режима распределения Azure Load Balancer | Документация Майкрософт
description: Сведения о том, как настроить режим распределения Azure Load Balancer для поддержки соответствия исходному IP-адресу.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ae793bad9cef86158418eb87e0c38ee0370a6bd2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176981"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Настройка режима распределения для Azure Load Balancer

## <a name="hash-based-distribution-mode"></a>Режим распространения на основе хэша

По умолчанию в качестве режима распределения Azure Load Balancer используется хэш с 5 кортежами. Кортеж состоит из исходного IP-адреса, исходного порта, целевого IP-адреса, конечного порта и типа протокола. Хэш используется для сопоставления трафика с доступными серверами, а алгоритм позволяет закреплять IP-адреса только в рамках сеанса транспорта. Пакеты в рамках одного сеанса будут направляться на один экземпляр IP-адреса центра обработки данных в конечной точке с балансировкой нагрузки. Когда клиент начинает новый сеанс с того же исходного IP-адреса, порт источника изменяется и перенаправляет трафик к другой конечной точке IP-адреса центра обработки данных.

![Режим распространения на основе хэша с 5 кортежами](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Режим сходства исходного IP-адреса

Для Load Balancer также можно настроить режим распределения соответствия исходному IP-адресу. Он также известен как соответствие сеансу или соответствие клиентскому IP-адресу. Этот режим использует хэш 2 кортежей (исходный IP-адрес, конечный порт) или хэш 3 кортежей (исходный IP-адрес, конечный порт и тип протокола) для сопоставления трафика с доступными серверами. При использовании соответствия исходному IP-адресу подключения, инициированные с одного клиентского компьютера, направляются к одной конечной точке DIP.

Конфигурация двухкортежного распределения представлена на следующем рисунке. Обратите внимание, как 2 кортежа передаются через подсистему балансировки нагрузки на виртуальную машину 1 (VM1). Затем VM1 архивируется на виртуальных машинах VM2 и VM3.

![Режим двухкортежного распределения соответствия сеансу](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Режим соответствия исходному IP-адресу позволяет устранить несовместимость Azure Load Balancer и шлюза удаленных рабочих столов. С помощью этого режима можно создать ферму шлюза удаленных рабочих столов в одной облачной службе.

Другой сценарий использования — передача мультимедиа. В этом случае передача данных осуществляется по протоколу UDP, а управление — по протоколу TCP.

* Сначала клиент инициирует сеанс TCP для общедоступного IP-адреса с балансировкой нагрузки, который затем направляется по конкретному DIP. Сам канал остается активным, чтобы контролировать состояние подключения.
* С того же клиентского компьютера для той же общедоступной конечной точки с балансировкой нагрузки инициируется новый сеанс UDP. Подключение направляется на ту же конечную точку DIP, что и предыдущее TCP-подключение. Таким образом можно передавать мультимедиа с более высокой пропускной способностью, поддерживая при этом канал управления по TCP.

> [!NOTE]
> При изменении набора балансировки нагрузки (при добавлении или удалении виртуальной машины) распределение запросов клиента вычисляется заново. Нельзя полагаться на то, что новые подключения от существующих клиентов будут направлены на один сервер. Кроме того, использование режима распределения соответствия исходному IP-адресу может привести к неравномерному распределению трафика. Клиенты, работающие на прокси-серверах, могут рассматриваться как одно уникальное клиентское приложение.

## <a name="configure-source-ip-affinity-settings"></a>Настройка параметров соответствия исходному IP-адресу

Для виртуальных машин можно использовать Azure PowerShell, чтобы изменить параметры времени ожидания: Добавьте конечную точку Azure в виртуальную машину и установите режим распределения балансировщика нагрузки.

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Установите для элемента `LoadBalancerDistribution` значение необходимого объема балансировки нагрузки. Укажите значение sourceIP для балансировки нагрузки по двум кортежам (исходный IP-адрес, конечный IP-адрес). Укажите sourceIPProtocol для балансировки нагрузки по трем кортежам (исходный IP-адрес, конечный IP-адрес, тип протокола). Укажите none для поведения по умолчанию (балансировка нагрузки по пяти кортежам).

Получите конфигурацию режима распределения балансировщика нагрузки для конечной точки с помощью этих параметров:

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

Если элемент `LoadBalancerDistribution` отсутствует, Azure Load Balancer использует алгоритм балансировки нагрузки по умолчанию (балансировка нагрузки по 5 кортежам).

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Настройка режима распределения для набора балансировки нагрузки для конечных точек

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует настроить режим распределения:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Настройка режима распределения для конечных точек облачной службы

Обновите облачную службу с помощью пакета SDK Azure для .NET 2.5. Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить режим распределения балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание.

Ниже приведен пример изменений в CSDEF-файле для настройки конечных точек:

```xml
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
```

## <a name="api-example"></a>Пример API

В следующем примере показано, как изменить режим распределения подсистемы балансировки нагрузки для заданного набора балансировки нагрузки в развертывании. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Изменение режима распределения развернутого набора балансировки нагрузки

Чтобы изменить существующую конфигурацию развертывания, используйте классическую модель развертывания Azure. Добавьте заголовок `x-ms-version` и задайте значение версии 2014-09-01 или более поздней.

#### <a name="request"></a>Запрос

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
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

Как описано выше, установите для элемента `LoadBalancerDistribution` значение sourceIP для соответствия по двум кортежам; sourceIPProtocol для соответствия по трем кортежам; none для отсутствия соответствия (соответствие по пяти кортежам).

#### <a name="response"></a>Ответ

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Дополнительная информация

* [Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)
* [Создание балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell](load-balancer-get-started-internet-arm-ps.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: "Настройка режима распределения балансировщика нагрузки | Документация Майкрософт"
description: "Как настроить режим распределения подсистемы балансировки нагрузки Azure для поддержки соответствия исходному IP-адресу"
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
ms.openlocfilehash: a6b3c346358e0aed4c60c4903932236edc237379
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-distribution-mode-for-load-balancer"></a>Настройка режима распределения для подсистемы балансировки нагрузки

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Режим распространения на основе хэша

В основе алгоритма распределения по умолчанию лежит использование хэша пяти кортежей (исходный IP-адрес, порт источника, IP-адрес назначения, порт назначения, тип протокола) для сопоставления трафика с доступными серверами. Он позволяет закреплять IP-адреса только в рамках сеанса транспорта. Пакеты в рамках одного сеанса будут направляться на один экземпляр IP-адреса центра обработки данных в конечной точке с балансировкой нагрузки. Когда клиент начинает новый сеанс с того же исходного IP-адреса, порт источника изменяется и перенаправляет трафик к другой конечной точке IP-адреса центра обработки данных.

![подсистема балансировки нагрузки на основе хэша](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Рис. 1. Пятикортежное распределение

## <a name="source-ip-affinity-mode"></a>Режим сходства исходного IP-адреса

Доступен новый режим распределения — соответствие исходному IP-адресу (также известный как соответствие сеансу или соответствие клиентскому IP-адресу). С помощью этого режима Azure Load Balancer можно настроить для использования двух кортежей (исходный IP-адрес, IP-адрес назначения) или трех кортежей (исходный IP-адрес, IP-адрес назначения, протокол), чтобы сопоставлять трафик с доступными серверами. При использовании соответствия исходному IP-адресу подключения, инициированные с одного клиентского компьютера, направляются к одной конечной точке DIP.

Конфигурация двухкортежного распределения представлена на следующей схеме. Обратите внимание, как два кортежа передаются через подсистему балансировки нагрузки на виртуальную машину 1 (VM1), а затем архивируются на виртуальных машинах VM2 и VM3.

![Сходство сеансов](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Рис. 2. Двухкортежное распределение

Режим соответствия исходному IP-адресу позволяет устранить несовместимость подсистемы Azure Load Balancer и шлюза удаленных рабочих столов. Теперь можно создать ферму шлюза удаленных рабочих столов в одной облачной службе.

Другой сценарий использования — передача мультимедиа, при которой передача данных осуществляется по протоколу UDP, а управление — по протоколу TCP.

* Сначала клиент инициирует сеанс TCP для общедоступного IP-адреса с балансировкой нагрузки. Этот запрос отправляется по конкретному DIP, а сам канал остается активным, чтобы контролировать состояние подключения.
* С того же клиентского компьютера для той же общедоступной конечной точки с балансировкой нагрузки инициируется новый сеанс UDP. Ожидается, что это подключение будет направлено на ту же конечную точку DIP, что и предыдущее TCP-подключение. Таким образом можно передавать мультимедиа с более высокой пропускной способностью, поддерживая при этом канал управления по TCP.

> [!NOTE]
> При изменении набора балансировки нагрузки (при добавлении или удалении виртуальной машины) распределение запросов клиента вычисляется заново. Нельзя полагаться на то, что новые подключения от существующих клиентов будут направлены на один сервер. Кроме того, использование режима распределения соответствия исходному IP-адресу может привести к неравномерному распределению трафика. Клиенты, работающие на прокси-серверах, могут рассматриваться как одно уникальное клиентское приложение.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Настройка параметров соответствия исходному IP-адресу для подсистемы балансировки нагрузки

Для виртуальных машин можно использовать PowerShell, чтобы изменить параметры времени ожидания:

Добавление конечной точки Azure в виртуальную машину и установка режима распределения балансировщика нагрузки

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Параметру LoadBalancerDistribution можно присвоить следующие значения: sourceIP для балансировки нагрузки по двум кортежам (исходный IP-адрес, IP-адрес назначения); sourceIPProtocol для балансировки нагрузки по трем кортежам (исходный IP-адрес, IP-адрес назначения, протокол); none для поведения по умолчанию (балансировка нагрузки по пяти кортежам).

Используйте следующий сценарий, чтобы получить конфигурацию режима распределения подсистемы балансировки нагрузки для конечной точки.

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

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Установка режима распределения для набора балансировки нагрузки для конечных точек

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует установить режим распределения:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Настройка облачной службы для изменения режима распределения

Вы можете обновить облачную службу с помощью пакета Azure SDK для .NET 2.5. Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить режим распределения балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание.
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

Можно настроить распределение балансировщика нагрузки с помощью API управления службами. Обязательно добавьте заголовок `x-ms-version`, для которого задана версия `2014-09-01` или более поздняя версия.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Обновление конфигурации указанного набора балансировки нагрузки в развертывании

#### <a name="request-example"></a>Пример запроса

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

Параметр LoadBalancerDistribution может иметь следующие значения: sourceIP для соответствия по двум кортежам; sourceIPProtocol для соответствия по трем кортежам; none (для отсутствия соответствия, т. е. балансировки по пяти кортежам).

#### <a name="response"></a>Ответ

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Дальнейшие действия

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-get-started-internet-arm-ps.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

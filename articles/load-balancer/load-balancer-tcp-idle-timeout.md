---
title: Настройка времени ожидания простоя TCP для балансировщика нагрузки | Документация Майкрософт
description: Описывается настройка времени ожидания простоя TCP для балансировщика нагрузки.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23020909"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Настройка параметров времени ожидания простоя TCP для Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

В конфигурации по умолчанию значение параметра времени ожидания простоя для балансировщика нагрузки Azure равно 4 минутам. Если период бездействия превышает значение времени ожидания, нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой возобновится.

При закрытии подключения клиентское приложение может получить такое сообщение об ошибке: "Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером".

Распространенной практикой является проверка активности TCP. Такой подход позволяет поддерживать подключения активными в течение более длительного периода. Дополнительные сведения вы найдете в следующих [примерах для .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Когда проверка активности включена, пакеты отправляются в периоды простоя подключений. Благодаря пакетам проверки активности значение времени ожидания простоя не достигается и подключение сохраняется в течение длительного времени.

Этот параметр действует только для входящих подключений. Чтобы избежать потери подключения, необходимо настроить проверку активности TCP с интервалом, который должен быть меньше, чем время ожидания простоя. Можно также увеличить значение времени ожидания простоя. Для поддержки таких сценариев мы добавили возможность настройки времени ожидания простоя. Теперь для него можно задать значение от 4 до 30 минут.

Проверки активности TCP хорошо подходят для сценариев, в которых нет ограничений, обусловленных временем работы аккумулятора. Не рекомендуется для мобильных приложений. Использование этого метода в мобильном приложении может привести к ускоренной разрядке аккумулятора устройства.

![Время ожидания TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

В приведенных ниже разделах описывается, как изменить параметры времени ожидания простоя в виртуальных машинах и облачных службах.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Настройка для времени ожидания TCP, равного 15 минутам, для общедоступного IP-адреса уровня экземпляра

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` является необязательным. Если значение параметра не задано, время ожидания по умолчанию будет составлять 4 минуты. Допустимый диапазон времени ожидания — от 4 до 30 минут.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Настройка времени ожидания простоя при создании конечной точки Azure в виртуальной машине

Чтобы изменить параметр времени ожидания для конечной точки, используйте следующее:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Чтобы получить конфигурацию времени ожидания простоя, используйте следующую команду:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Настройка времени ожидания TCP для набора конечных точек с балансировкой нагрузки

Если набор балансировки нагрузки для конечных точек содержит конечные точки, для него следует установить время ожидания TCP. Например: 

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Изменение параметров времени ожидания для облачных служб

Вы можете обновить облачную службу с помощью пакета Azure SDK. Параметры конечных точек для облачных служб можно настроить в CSDEF-файле. Чтобы обновить время ожидания TCP для развертывания облачной службы, требуется обновить развертывание. Исключением является случай, когда время ожидания TCP задано только для общедоступного IP-адреса. Параметры общедоступного IP-адреса находятся в CSCFG-файле, и их можно обновить в процессе обновления развертывания и установки новой версии.

Ниже перечислены изменения в настройках конечной точки в CSCFG-файле:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Изменения CSCFG-файла для параметров времени ожидания общедоступных IP-адресов приведены ниже.

```xml
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

## <a name="rest-api-example"></a>Пример для REST API

Можно настроить время ожидания простоя TCP с помощью API управления службами. Убедитесь, что для заголовка `x-ms-version` задана версия `2014-06-01` или более поздняя. Обновите конфигурацию указанных конечных точек ввода с балансировкой нагрузки для всех виртуальных машин в развертывании.

### <a name="request"></a>Запрос

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Ответ

```xml
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
```

## <a name="next-steps"></a>Дополнительная информация

[Обзор внутренней подсистемы балансировки нагрузки](load-balancer-internal-overview.md)

[Приступая к настройке балансировщика нагрузки для Интернета](load-balancer-get-started-internet-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

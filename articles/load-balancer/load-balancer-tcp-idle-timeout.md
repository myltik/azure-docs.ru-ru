<properties
   pageTitle="Настройка времени ожидания простоя TCP для балансировщика нагрузки | Microsoft Azure"
   description="Описывается настройка времени ожидания простоя TCP для балансировщика нагрузки."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />

# Изменение параметров времени ожидания простоя TCP для балансировщика нагрузки

В конфигурации по умолчанию значение параметра времени ожидания простоя для балансировщика нагрузки Azure равно 4 минутам.

Это означает, что если период бездействия превышает значение времени ожидания, то нет никакой гарантии, что сеанс TCP или HTTP между клиентом и облачной службой все еще существует.

При закрытии подключения клиентское приложение получит сообщение об ошибке следующего вида: "Базовое соединение закрыто. Соединение, которое должно было работать, было разорвано сервером".

Распространенный метод для поддержки подключения в активном состоянии в течение длительного периода — проверка активности TCP. (Вы можете ознакомиться с [примерами для .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx).) При использовании этого метода в случае отсутствия активности подключения отправляются пакеты. Благодаря сетевой активности значение времени ожидания простоя не достигается и подключение сохраняется в течение длительного времени.

Чтобы избежать потери подключения, необходимо настроить проверку активности TCP с интервалом, который должен быть меньше, чем время ожидания простоя. Можно также увеличить значение времени ожидания простоя.

Проверка активности TCP хорошо подходит для сценариев без ограничений, связанных с аккумуляторами. Однако этот метод, как правило, не рекомендуется для мобильных приложений. Использование этого метода в мобильном приложении может привести к ускоренной разрядке аккумулятора устройства.

Для поддержки таких сценариев мы добавили возможность настройки времени ожидания простоя. Теперь для него можно задать значение от 4 до 30 минут. Этот параметр действует только для входящих подключений.

![Время ожидания TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

В приведенных ниже разделах описывается, как изменить параметры времени ожидания простоя в виртуальных машинах и облачных службах.

>[AZURE.NOTE] Чтобы иметь возможность настраивать эти параметры, убедитесь, что вы установили последнюю версию пакета Azure PowerShell.

## Настройка для времени ожидания TCP, равного 15 минутам, для общедоступного IP-адреса уровня экземпляра

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

Параметр `IdleTimeoutInMinutes` является необязательным. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты.

>[AZURE.NOTE] Допустимый диапазон времени ожидания — от 4 до 30 минут.

## Настройка времени ожидания простоя при создании конечной точки Azure в виртуальной машине

Измените параметр времени ожидания для конечной точки.

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Получите конфигурацию времени ожидания простоя.

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

## Настройка времени ожидания TCP для набора конечных точек с балансировкой нагрузки

Если конечные точки входят в набор конечных точек с балансировкой нагрузки, то для него следует установить время ожидания TCP.

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Изменение параметров времени ожидания для облачных служб

Вы можете обновить облачную службу с помощью пакета SDK Azure для .NET 2.4.

Параметры конечных точек для облачных служб можно настроить в CSDEF-файле. Чтобы обновить время ожидания TCP для развертывания облачной службы, требуется обновить развертывание. Исключением является случай, когда время ожидания TCP задано только для общедоступного IP-адреса. Параметры общедоступного IP-адреса находятся в CSCFG-файле, и их можно обновить в процессе обновления развертывания и установки новой версии.

Ниже перечислены изменения в настройках конечной точки в CSCFG-файле:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Изменения CSCFG-файла для параметров времени ожидания общедоступных IP-адресов приведены ниже.

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

## Пример для REST API

Можно настроить время ожидания простоя TCP с помощью API управления службами. Убедитесь, что для заголовка x-ms-version задана версия 2014-06-01 или более поздняя версия.

Обновите конфигурацию указанных конечных точек ввода с балансировкой нагрузки для всех виртуальных машин в развертывании.

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

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

[Приступая к настройке балансировщика нагрузки для Интернета](load-balancer-get-started-internet-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->
<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Управление: время ожидания простоя балансировщика нагрузки" 
   description="Функции управления для времени ожидания простоя балансировщика нагрузки Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# Управление виртуальной сетью: время ожидания простоя TCP балансировщика нагрузки

**Время ожидания простоя TCP** позволяет разработчику указать гарантированное пороговое значение периода бездействия во время сеансов «клиент-сервер» с использованием балансировщика нагрузки Azure. Значение времени ожидания простоя TCP 4 минуты (значение по умолчанию для балансировщика нагрузки Azure) означает, что если период бездействия во время сеанса «клиент-сервер» с использованием балансировщика нагрузки Azure длится дольше 4 минут, подключение будет закрыто.

При закрытии подключения «клиент-сервер» клиентское приложение получит сообщение об ошибке, аналогичное следующему: «Базовое соединение закрыто: соединение, которое должно было работать, было разорвано сервером».

[Проверка активности TCP](http://tools.ietf.org/html/rfc1122#page-101) — стандартный метод для поддержки подключений в активном состоянии в течение длительного периода бездействия [(пример MSDN)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). При использовании проверки активности TCP клиент периодически отправляет простые пакеты (обычно с частотой, меньшей порогового значения времени ожидания простоя сервера). Сервер считает такие передачи свидетельством активности подключения даже при отсутствии других действий. Таким образом, значение времени ожидания простоя никогда не достигается, а подключение может поддерживаться в течение длительного периода времени.

Несмотря на эффективность проверки активности TCP, ее, как правило, нельзя использовать для мобильных приложений, так как она использует ограниченные ресурсы питания мобильных устройств. При использовании проверки активности TCP в мобильных приложениях заряд батареи устройства исчерпывается намного быстрее, так как при такой проверке постоянно потребляется энергия для использования сети.

Для поддержки сценариев мобильных устройств балансировщик нагрузки Azure поддерживает настраиваемое время ожидания простоя TCP. Разработчики могут задавать время ожидания активности TCP любой длительности от 4 до 30 минут для входящих подключений (настраиваемое время ожидания простоя TCP не применяется к исходящим подключениям). Это позволяет клиентам проводить более длительные сеансы с использованием серверов с длительным периодом бездействия. Приложение на мобильном устройстве по-прежнему может использовать метод проверки активности TCP для сохранения ожидающих подключений с периодом бездействия более 30 минут, но из-за настолько длительного времени ожидания простоя TCP запросы проверки активности TCP возникают намного реже, чем раньше, что значительно снижает нагрузку на ресурсы питания мобильных устройств.

## Реализация

Можно настроить время ожидания простоя TCP для:

* [общедоступных IP-адресов на уровне экземпляра;](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [наборов балансировки нагрузки для конечных точек;](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [конечных точек виртуальных машин;](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [веб-ролей;](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [рабочих ролей.](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Дальнейшие действия
* ПОДЛЕЖИТ УТОЧНЕНИЮ

## Примеры для PowerShell
Рекомендуем скачать [последний выпуск Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases).

### Настройка для времени ожидания TCP для общедоступного IP-адреса на уровне экземпляра значения 15 минут

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes — необязательный параметр. Если он не задан, время ожидания по умолчанию будет составлять 4 минуты. Теперь для него можно установить значение от 4 до 30 минут.

### Установка времени ожидания простоя при создании конечной точки Azure в виртуальной машине

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Получение конфигурации времени ожидания простоя

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

## Примеры с облачной службой

Вы можете обновить облачную службу с помощью пакета SDK для Azure для .NET.

Конечные точки для облачных служб можно настроить в CSDEF-файле. Чтобы обновить время ожидания TCP балансировщика нагрузки для развертывания облачных служб, нужно обновить развертывание. Исключение применяется, если время ожидания TCP задано только для общедоступного IP-адреса. Параметры общедоступного IP-адреса находятся в CSCFG-файле, и их можно обновить в процессе обновления развертывания и обновления до следующей версии.

Ниже перечислены изменения в настройках конечной точки в CSCFG-файле:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

Разработчики могут настроить распределение балансировщика нагрузки с помощью API управления службами. Обязательно добавьте заголовок x-ms-version, для которого задана версия 01.06.2014 или более поздняя версия.

### Обновление конфигурации указанных конечных точек ввода с балансировкой нагрузки для всех виртуальных машин развертывания

#### Запрос

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

LoadBalancerDistribution может иметь значение sourceIP для соответствия по 2 кортежам, sourceIPProtocol для соответствия по 3 кортежам или none (для отсутствия соответствия, т. е. балансировки по 5 кортежам).

#### Ответ

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
 

<!---HONumber=July15_HO4-->
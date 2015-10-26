<properties
   pageTitle="Ресурсы манифеста служб Service Fabric"
   description="Описание ресурсов в манифесте служб"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="anuragg"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# Ресурсы манифеста служб

## Обзор

Манифест служб позволяет использовать ресурсы в службе для объявления или изменения, не меняя скомпилированный код. Service Fabric поддерживает настройку ресурсов конечных точек для службы. Доступ к ресурсам, указанным в манифесте служб, можно контролировать с помощью SecurityGroup в манифесте приложения. Объявление ресурсов позволяет изменить во время развертывании. При этом службе не требуется внедрять новый механизм настройки.

## Конечные точки

Когда ресурс конечной точки определяется в манифесте служб, Service Fabric назначает порты из диапазона портов зарезервированных приложений. Кроме того, службы также могут запрашивать наличие в ресурсе конкретного порта. Репликам службы, которые выполняются на различных узлах кластера, можно назначить другие номера портов, а реплики этой же службы, выполняющиеся на аналогичном узле, будут использовать тот же порт. Такие порты могут использоваться репликами службы для различных целей, таких как репликация, прослушивание клиентских запросов и т. д.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Дополнительные сведения о создании ссылок на конечные точки из файла параметров пакета конфигурации (settings.xml) см. в статье [Настройка надежных служб с отслеживанием состояния](../Service-Fabric/service-fabric-reliable-services-configuration.md).

## Образец
В следующем манифесте служб определяется 1 ресурс конечной точки TCP и 2 ресурса конечной точки HTTP в элементе &lt;Resources&gt;.

Service Fabric автоматически создает список управления доступом (ACL) для конечных точек HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=Oct15_HO3-->
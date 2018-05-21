---
title: Защита взаимодействия служб в Azure Service Fabric | Документация Майкрософт
description: Общие сведения о способах защиты взаимодействия служб Reliable Services, выполняющихся в кластере Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 624d9d358145fb8b41013d686821cb157693d3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Защита взаимодействия служб в Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java в Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Защита службы при использовании удаленного взаимодействия служб
Мы будем использовать существующий [пример](service-fabric-reliable-services-communication-remoting-java.md) , в котором описывается настройка удаленного взаимодействия для Reliable Services. Для защиты службы при использовании удаленного взаимодействия служб выполните следующие действия:

1. Создайте интерфейс `HelloWorldStateless`, определяющий методы, которые будут доступны для удаленного вызова процедур в службе. Служба также будет использовать прослушиватель `FabricTransportServiceRemotingListener`, который объявляется в пакете `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Это реализация `CommunicationListener` , которая предоставляет возможности удаленного взаимодействия.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Добавьте параметры прослушивателя и учетные данные безопасности.

    Убедитесь, что сертификат, который вы хотите использовать для защиты взаимодействия со службой, установлен на всех узлах в кластере. Существует два способа указания параметров прослушивателя и учетных данных безопасности:

   1. Предоставьте их с помощью [пакета конфигурации](service-fabric-application-and-service-manifests.md):

       Добавьте раздел `TransportSettings` в файл settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       В этом случае метод `createServiceInstanceListeners` будет выглядеть следующим образом:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        При добавлении раздела `TransportSettings` в файл settings.xml без префикса `FabricTransportListenerSettings` по умолчанию загрузит все параметры из этого раздела.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        В этом случае метод `CreateServiceInstanceListeners` будет выглядеть следующим образом:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. При вызове методов для защищенной службы с помощью стека удаленного взаимодействия вместо использования класса `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` для создания прокси-службы используйте `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Если клиентский код выполняется как часть службы, можно загрузить `FabricTransportSettings` из файла settings.xml. Создайте раздел TransportSettings по аналогии с кодом службы, как показано выше. Внесите следующие изменения в клиентский код:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```

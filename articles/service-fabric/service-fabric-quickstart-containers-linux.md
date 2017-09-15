---
title: "Создание приложения-контейнера Azure Service Fabric в Linux | Документация Майкрософт"
description: "Создание первого приложения-контейнера Linux в Azure Service Fabric.  Создание образа Docker с приложением, отправка образа в реестр контейнеров, сборка и развертывание приложения-контейнера Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Развертывание приложения-контейнера Service Fabric для Linux в Azure
Azure Service Fabric — это платформа распределенных систем для развертывания масштабируемых надежных микрослужб и контейнеров и управления ими. 

Чтобы запустить существующее приложение в контейнере Linux кластера Service Fabric не требуется вносить изменения в приложение. В этом кратком руководстве показано, как развернуть готовый образ контейнера Docker в приложении Service Fabric. По окончании вы получите рабочий контейнер nginx.  В этом кратком руководстве объясняется, как развернуть контейнер Linux. Чтобы развернуть контейнер Windows, ознакомьтесь с [этим кратким руководством](service-fabric-quickstart-containers.md).

![Nginx][nginx]

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * упаковка контейнера образов Docker;
> * Настройка обмена данными
> * Создание и упаковка приложений Service Fabric
> * развертывание приложения-контейнера в Azure.

## <a name="prerequisites"></a>Предварительные требования
Установите [пакет SDK для Service Fabric, интерфейс командной строки Service Fabric и генераторы шаблонов Yeoman для Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Упаковка контейнера образов Docker с помощью Yeoman
Пакет SDK Service Fabric для Linux включает в себя генератор [Yeoman](http://yeoman.io/), который упрощает создание приложения и добавление образа контейнера. 

Чтобы создать приложение-контейнер Service Fabric, откройте окно терминала и выполните `yo azuresfcontainer`.  

Присвойте приложению имя MyFirstContainer, а службе приложения — MyContainerService.

Для образа контейнера задайте имя nginx:latest ([образ контейнера nginx](https://hub.docker.com/r/_/nginx/) в концентраторе Docker). 

В этом образе определена точка входа для рабочей нагрузки, поэтому необходимо явно указать команды ввода. 

Укажите число экземпляров — 1.

![Генератор Service Fabric Yeoman для контейнеров][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Настройка обмена данными и сопоставления порта контейнера с портом узла
Настройте конечную точку HTTP, чтобы клиенты могли обмениваться данными со службой.  Откройте файл *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* и объявите ресурс конечной точки в элементе **ServiceManifest**.  Добавьте протокол, порт и имя. В этом кратком руководстве служба ожидает передачи данных на порту 80. 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Если указать `UriScheme`, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Полный пример файла ServiceManifest.xml приведен в конце этой статьи. 

Сопоставьте порт контейнера с точкой `Endpoint` службы, используя политику `PortBinding`, заданную в разделе `ContainerHostPolicies` файла ApplicationManifest.xml.  В этом кратком руководстве для параметра `ContainerPort` задано значение 80 (контейнер предоставляет порт 80), а для `EndpointRef` — myserviceTypeEndpoint (конечная точка, определенная ранее в манифесте службы).  Входящие запросы к службе через порт 80 сопоставляются с портом 80 в контейнере.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Создание и упаковка приложений Service Fabric
Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала. Сохраните изменения.  Используйте следующую команду, чтобы собрать и упаковать приложение:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Создание кластера
Для развертывания приложения в кластере Azure можно создать собственный кластер или использовать кластер сообщества.

Кластеры сообщества — это бесплатные кластеры Service Fabric, которые доступны в течение ограниченного времени. Эти кластеры размещены в Azure и поддерживаются командой Service Fabric. Любой пользователь может развертывать приложения на этих кластерах и знакомиться с платформой. Чтобы получить доступ к кластеру сообщества, следуйте инструкциям на [этом сайте](http://aka.ms/tryservicefabric).  

Сведения о создании собственного кластера см. в разделе [Создание первого кластера Service Fabric в Azure](service-fabric-get-started-azure-cluster.md).

Запишите конечную точку подключения. Она понадобится вам на следующем шаге.

## <a name="deploy-the-application-to-azure"></a>Развертывание приложения в Azure
Созданное приложение можно развернуть в кластере Azure с помощью интерфейса командной строки Service Fabric.

Подключитесь к кластеру Service Fabric в Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

```bash
./install.sh
```

Откройте браузер и перейдите к обозревателю Service Fabric Explorer по адресу http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Разверните узел приложения. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

![Service Fabric Explorer][sfx]

Подключитесь к запущенному контейнеру.  Откройте в веб-браузере IP-адрес, возвращаемый через порт 80, например lnxt10vkfz6.westus.cloudapp.azure.com:80. В браузере откроется страница приветствия nginx.

![Nginx][nginx]

## <a name="clean-up"></a>Очистка
Чтобы удалить экземпляр приложения из кластера и отменить регистрацию типа приложения, используйте скрипт удаления, предоставленный в шаблоне.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Полные примеры манифестов службы и приложения Service Fabric
Ниже приведены полные коды манифестов службы и приложения, используемых в этом кратком руководстве.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Дальнейшие действия
Из этого краткого руководства вы узнали, как выполнять следующие задачи:
> [!div class="checklist"]
> * упаковка контейнера образов Docker;
> * Настройка обмена данными
> * Создание и упаковка приложений Service Fabric
> * развертывание приложения-контейнера в Azure.

* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-containers-overview.md).
* Ознакомьтесь с руководством [Развертывание приложения-контейнера .NET в Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* Извлечение [примеров кода контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) на GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png


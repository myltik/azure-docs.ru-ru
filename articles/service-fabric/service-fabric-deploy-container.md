---
title: Service Fabric и развертывание контейнеров | Microsoft Docs
description: Service Fabric и использование контейнеров для развертывания приложений микрослужб. В этой статье представлены возможности, которые предоставляет Service Fabric для контейнеров, а также описано развертывание образа контейнера в кластере
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-deploy-a-container-to-service-fabric"></a>Предварительная версия: развертывание контейнера в Service Fabric
> [!NOTE]
> Эта функция доступна в режиме предварительной версии для Linux и пока недоступна для Windows Server. Предварительная версия для Windows Server будет доступна в следующем выпуске Service Fabric после выпуска общедоступной версии Windows Server 2016. Она также будет поддерживаться в последующем выпуске.
> 
> 

В Service Fabric реализовано несколько способов использования контейнеров для создания приложений, состоящих из контейнерных микрослужб. Они называются контейнерными службами, которые включают следующие возможности:

* развертывание и активация образа контейнера;
* управление ресурсами;
* проверка подлинности репозитория;
* сопоставление порта контейнера с портом узла;
* межконтейнерное обнаружение и взаимодействие;
* возможность настройки и установки переменных среды.

Давайте поочередно рассмотрим все эти возможности на примере контейнерной службы, которая будет включена в приложение.

## <a name="packaging-a-container"></a>Упаковка контейнера
При упаковке контейнера у вас есть выбор — использовать шаблон проекта Visual Studio или [создать пакет приложения вручную](#manually). При использовании Visual Studio мастер проекта создает структуру пакета приложения и файлы манифеста за вас.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Упаковка существующего исполняемого приложения с использованием Visual Studio
> [!NOTE]
> В одном из будущих выпусков пакета SDK для средств Visual Studio появится возможность добавлять к приложению контейнер так же, как сейчас можно добавить гостевой исполняемый файл. См. статью [Развертывание гостевого исполняемого файла в Service Fabric](service-fabric-deploy-existing-app.md). Сейчас для этого нужно выполнить упаковку вручную, как описано ниже.
> 
> 

<a id="manually"></a>

## <a name="manually-packaging-and-deploying-container"></a>Упаковка и развертывание контейнера вручную
Упаковка контейнерной службы вручную включает следующие этапы.

1. Публикация контейнеров в репозитории.
2. Создание структуры каталогов пакета.
3. Редактирование файла манифеста службы.
4. Редактирование файла манифеста приложения.

## <a name="container-image-deployment-and-activation."></a>Развертывание и активация образа контейнера.
В [модели приложения](service-fabric-application-model.md)Service Fabric контейнер представляет собой узел приложения, в котором размещается несколько реплик службы. Чтобы развернуть и активировать контейнер, вставьте имя образа контейнера в элемент `ContainerHost` в манифесте служб.

В манифесте служб добавьте значение `ContainerHost` в качестве точки входа, а также значение `ImageName` для имени репозитория и образа контейнера. Следующий фрагмент манифеста содержит пример развертывания контейнера с именем *myimage:v1* из репозитория, который называется *myrepo*.

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Входные команды для образа контейнера можно передать с помощью необязательного элемента `Commands` в формате разделенного запятыми набора команд, которые нужно выполнить в самом контейнере. 

## <a name="resource-governance"></a>управление ресурсами;
Контейнер позволяет управлять ресурсами, то есть вы можете указать, какие ресурсы контейнер может использовать на узле. Параметр `ResourceGovernancePolicy`, определяемый в манифесте приложения, позволяет объявить ограничения для ресурсов, доступных из пакета кода службы. Можно задать ограничения для следующих ресурсов:

* Память
* MemorySwap;
* CpuShares;
* MemoryReservationInMB;  
* BlkioWeight. 

> [!NOTE]
> В будущих версиях может быть добавлена поддержка ограничений на определенные параметры блочного ввода-вывода, например на количество операций ввода-вывода, скорость чтения/записи и т. п.
> 
> 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Проверка подлинности репозитория
Для загрузки контейнера могут потребоваться учетные данные для входа в репозиторий контейнера. Учетные данные для входа, указанные в манифесте *application* , представляют имя и пароль для входа или ключ SSH, которые позволят скачать образа контейнера из репозитория образов.  В следующем примере представлена учетная запись с именем *TestUser* и паролем в виде открытого текста. Мы рекомендуем **не** использовать такой формат.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Пароль можно и нужно зашифровать с помощью сертификата, развернутого на компьютере.

В следующем примере представлена учетная запись с именем *TestUser* и паролем, зашифрованным с помощью сертификата с именем *MyCert*. Можно использовать команду Powershell `Invoke-ServiceFabricEncryptText` для создания секретного текста зашифрованного пароля. Этот процесс описан в статье [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md) . Чтобы расшифровывать такой пароль, на локальном компьютере следует развернуть закрытый ключ сертификата по внештатному каналу (в Azure для этого используется Resource Manager). Тогда Service Fabric сможет расшифровать секретный код при развертывании пакета службы на компьютере и использовать его вместе с именем учетной записи для проверки подлинности в репозитории контейнера.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>сопоставление порта контейнера с портом узла;
С помощью `PortBinding` в манифесте приложения можно указать порт узла, который будет использоваться для обмена данными с контейнером. Привязка порта сопоставляет определенный порт, прослушиваемый службой в контейнере, с портом на узле.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Межконтейнерное обнаружение и взаимодействие
С помощью политики `PortBinding` можно сопоставить порт контейнера с конечной точкой `Endpoint` в манифесте службы, как показано в следующем примере. Конечная точка `Endpoint1` может определять фиксированный порт, например порт 80. Если не указать здесь номер порта, будет автоматически выбран случайный порт из диапазона портов для приложений кластера.

Если указать таким образом `Endpoint` для гостевых контейнеров в манифесте служб, Service Fabric автоматически опубликует указанную конечную точку в службе именования, чтобы другие службы в этом кластере могли обнаружить этот контейнер с помощью запросов REST к службе разрешения имен. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Зарегистрировав контейнер в службе именования, вы сможете легко выполнять в контейнере межконтейнерное взаимодействие в коде с использованием [обратного прокси-сервера](service-fabric-reverseproxy.md). Для этого достаточно указать в переменных среды http-порт прослушивания обратного прокси-сервера и имена служб, с которыми будет выполняться обмен данными. Этот процесс описан в следующем разделе.  

## <a name="configure-and-set-environment-variables"></a>Настройка и установка переменных среды
Переменные среды можно указывать в манифесте служб отдельно для каждого пакета кода — как для служб, развернутых в контейнере, так и для процессов или гостевых исполняемых файлов. Указанные значения переменных среды можно переопределить в манифесте приложения или указать как параметры приложения во время развертывания.

Следующий фрагмент XML-кода из манифеста службы демонстрирует, как определить переменные среды для пакета кода. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Эти переменные среды можно переопределить на уровне манифеста приложения:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

В приведенном выше примере мы присвоили явное значение (19000) переменной среды `HttpGateway`, а значение для параметра `BackendServiceName` указали через параметр приложения `[BackendSvc]`. Это позволяет указать значение для `BackendServiceName`во время развертывания приложения, а не определять фиксированное значение в манифесте. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Полные примеры манифестов для приложения и службы
Ниже приведен пример манифеста приложения, демонстрирующий функции контейнера.

    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


Ниже приведен пример манифеста службы (указанной в предложенном выше манифесте приложения), демонстрирующий функции контейнера.

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->



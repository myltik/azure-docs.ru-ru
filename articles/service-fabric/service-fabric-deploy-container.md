---
title: "Service Fabric и развертывание контейнеров | Документация Майкрософт"
description: "Service Fabric и использование контейнеров для развертывания приложений микрослужб. В этой статье рассмотрены возможности, которые платформа Service Fabric предоставляет для контейнеров. Также здесь описано развертывание образа контейнера Windows в кластере."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25d6b056421e71fa70ed20a39589f77dbbc25c69
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-a-windows-container-to-service-fabric"></a>Развертывание контейнера Windows в Service Fabric
> [!div class="op_single_selector"]
> * [Развертывание контейнера Windows](service-fabric-deploy-container.md)
> * [Развертывание контейнера Docker](service-fabric-deploy-container-linux.md)
> 
> 

В этой статье подробно рассматривается создание контейнерных служб в контейнерах Windows.

В Service Fabric реализовано несколько возможностей для создания приложений, состоящих из микрослужб, выполняющихся в контейнерах. 

и включают следующие возможности:

* развертывание и активация образа контейнера;
* управление ресурсами;
* проверка подлинности репозитория;
* сопоставление порта контейнера с портом узла;
* межконтейнерное обнаружение и взаимодействие;
* возможность настройки и установки переменных среды.

Давайте рассмотрим все эти возможности на примере упаковки контейнерной службы, которая будет включена в приложение.

## <a name="package-a-windows-container"></a>Упаковка контейнера Windows
При упаковке контейнера вы можете использовать шаблон проекта Visual Studio или [создать пакет приложения вручную](#manually).  Если вы используете Visual Studio, шаблон проекта создаст для вас структуру пакета приложения и файлы манифеста.

> [!TIP]
> Упаковать имеющийся образ контейнера в службу проще всего с помощью Visual Studio.

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Упаковка существующего образа контейнера с помощью Visual Studio
Visual Studio предоставляет шаблон службы Service Fabric для развертывания контейнера файла в кластере Service Fabric.

1. Чтобы создать приложение Service Fabric, выберите **Файл** > **Создать проект**.
2. Выберите **гостевой контейнер** в качестве шаблона службы.
3. Выберите **имя образа** и укажите путь к образу в репозитории контейнера, например `myrepo/myimage:v1` в https://hub.docker.com.
4. Присвойте службе имя и нажмите кнопку **ОК**.
5. Если контейнерной службе нужна конечная точка для обмена данными, можно добавить значения протокола, порта и типа в файл ServiceManifest.xml. Например: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    Если указать `UriScheme`, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Порт может быть фиксированным (как показано в приведенном выше примере) или выделяться динамически. Если не указать порт, он динамически выделяется из диапазона портов приложения (как и в случае с любой службой).
    Кроме того, необходимо настроить сопоставление порта контейнера с портом узла, указав политику `PortBinding` в манифесте приложения. Дополнительные сведения см. в разделе [Настройка сопоставления порта контейнера с портом узла](#Portsection).
6. Если для контейнера требуется управление ресурсами, добавьте `ResourceGovernancePolicy`.
8. Если для контейнера требуется проверка подлинности в частном репозитории, добавьте `RepositoryCredentials`.
7. Если используется компьютер Windows Server 2016 с активированной поддержкой контейнеров, вы можете использовать пакет и опубликовать действие, чтобы выполнить развертывание в локальном кластере. 
8. Когда все будет готово, можно опубликовать приложение на удаленном кластере или вернуть решение в систему управления версиями. 

В качестве примера можно ознакомиться с [примерами кода контейнера Service Fabric на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="creating-a-windows-server-2016-cluster"></a>Создание кластера Windows Server 2016
Чтобы развернуть контейнерное приложение, необходимо создать кластер под управлением Windows Server 2016 с поддержкой контейнеров. Кластер может выполняться локально или быть развернут с использованием Azure Resource Manager в Azure. 

Чтобы развернуть кластер с помощью Azure Resource Manager, выберите вариант образа **Windows Server 2016 with Containers** (Windows Server 2016 с контейнерами) в Azure. Ознакомьтесь со статьей [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Используйте приведенные ниже параметры Azure Resource Manager.

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
Для создания кластера можно также использовать [шаблон Azure Resource Manager кластера с 5 узлами](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype). Кроме того, можно прочитать [записи блога сообщества](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html) об использовании Service Fabric и контейнеров Windows.

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Упаковка и развертывание образа контейнера вручную
Упаковка контейнерной службы вручную включает следующие этапы.

1. Публикация контейнеров в репозиторий.
2. Создание структуры каталогов пакета.
3. Редактирование файла манифеста службы.
4. Редактирование файла манифеста приложения.

## <a name="deploy-and-activate-a-container-image"></a>Развертывание и активация образа контейнера
В [модели приложения](service-fabric-application-model.md)Service Fabric контейнер представляет собой узел приложения, в котором размещается несколько реплик службы. Чтобы развернуть и активировать контейнер, вставьте имя образа контейнера в элемент `ContainerHost` в манифесте служб.

Добавьте в манифест службы элемент `ContainerHost` для точки входа. Затем установите `ImageName` в качестве имени репозитория и образа контейнера. Следующий фрагмент манифеста содержит пример развертывания контейнера с именем `myimage:v1` из репозитория с именем `myrepo`.

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

Вы можете указать в элементе `Commands` необязательные команды, которые будут выполняться при запуске контейнера. Команды нужно разделить запятыми. 

## <a name="understand-resource-governance"></a>Концепция управления ресурсами
Управление ресурсами подразумевает, что вы можете определять ресурсы, которые контейнер может использовать на узле. Параметр `ResourceGovernancePolicy`, определяемый в манифесте приложения, позволяет объявить ограничения для ресурсов, доступных из пакета кода службы. Ограничения ресурсов можно задать для следующих ресурсов:

* Память
* MemorySwap;
* CpuShares;
* MemoryReservationInMB;  
* BlkioWeight.

> [!NOTE]
> Поддержка ограничений на определенные параметры блочного ввода-вывода, включая число операций ввода-вывода, скорость чтения-записи и т. п., планируется в будущих выпусках.
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Проверка подлинности в репозитории
Для скачивания контейнера вам могут понадобится учетные данные для входа в репозиторий контейнера. Учетные данные для входа, указанные в манифесте приложения, определяют имя и пароль для входа (или ключ SSH). Они нужны для скачивания образа контейнера из репозитория образов. В следующем примере представлена учетная запись с именем *TestUser* и паролем в виде открытого текста (такая практика *не рекомендуется*).

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Мы советуем шифровать пароль с использованием сертификата, развернутого на компьютере.

В следующем примере представлена учетная запись с именем *TestUser* и паролем, зашифрованным с помощью сертификата с именем *MyCert*. Можно использовать команду PowerShell `Invoke-ServiceFabricEncryptText` для создания секретного текста зашифрованного пароля. Этот процесс описан в статье [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md).

На локальном компьютере следует развернуть закрытый ключ сертификата, который позволит расшифровывать на нем пароль, с использованием внешних средств. (В Azure для этого используется Azure Resource Manager.) Когда Service Fabric развернет на компьютере пакет службы, секретный код можно будет расшифровать, а затем выполнить проверку подлинности в репозитории контейнера, используя этот секрет и имя учетной записи.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name ="Portsection"></a> Настройка сопоставления порта контейнера с портом узла
С помощью политики `PortBinding` в манифесте приложения можно указать порт узла, который будет использоваться для обмена данными с контейнером. Это сопоставление связывает порт, который прослушивает служба в контейнере, с портом на узле.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Настройка межконтейнерного поиска и обмена данными
Используйте элемент `PortBinding` для сопоставления порта контейнера с конечной точкой в манифесте службы. В указанном ниже примере конечная точка `Endpoint1` указывает фиксированный порт 8905. Если не указать здесь номер порта, будет автоматически выбран случайный порт из диапазона портов для приложений кластера.


```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```
Если указать конечную точку с помощью тега `Endpoint` в манифесте службы для гостевого контейнера, Service Fabric может автоматически опубликовать эту конечную точку в службе именования. Это позволит другим службам, которые выполняются в кластере, находить этот контейнер с помощью запросов REST.

Зарегистрировав контейнер в службе именования, вы выполните обмен данными между контейнерами в своем контейнере, используя [обратный прокси-сервер](service-fabric-reverseproxy.md). Для этого достаточно указать в переменных среды http-порт прослушивания обратного прокси-сервера и имена служб, с которыми будет выполняться обмен данными. Этот процесс описан в следующем разделе. 

## <a name="configure-and-set-environment-variables"></a>Настройка и установка переменных среды
Переменные среды можно указать для каждого пакета кода в манифесте служб. Эта функция доступна для всех служб, вне зависимости от того, как они развернуты: в качестве контейнеров, процессов или гостевых исполняемых файлов. Вы можете переопределить значения переменных среды в манифесте приложения или указать их в качестве параметров приложения во время развертывания.

Следующий фрагмент XML-кода из манифеста службы демонстрирует, как определить переменные среды для пакета кода.

```xml
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
```

Эти переменные среды можно переопределить на уровне манифеста приложения:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

В приведенном выше примере мы указали явное значение (19000) для переменной среды `HttpGateway`. Значение для параметра `BackendServiceName` мы указали с помощью параметра приложения `[BackendSvc]`. Так мы можем указать значение для `BackendServiceName` при развертывании приложения, а не использовать фиксированное значение в манифесте.

## <a name="configure-isolation-mode"></a>Настройка режима изоляции

Windows поддерживает два режима изоляции для контейнеров: режим изоляции процессов и Hyper-V.  В режиме изоляции процесса все контейнеры, запущенные на одном хост-компьютере, совместно используют ядро и узел. В режиме изоляции Hyper-V все контейнеры Hyper-V и узлы контейнера используют отдельные ядра. Режим изоляции указывается в теге `ContainerHostPolicies` в файле манифеста приложения.  Вы можете указать следующие режимы изоляции: `process`, `hyperv` и `default`. Режим изоляции `default` на узлах Windows Server по умолчанию имеет значение `process`, а на узлах Windows 10 значение `hyperv`.  В указанном ниже фрагменте кода показано, как режим изоляции указывается в файле манифеста приложения.

```xml
   <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
```


## <a name="complete-examples-for-application-and-service-manifest"></a>Полные примеры манифестов для приложения и службы

Ниже приведен пример манифеста приложения.

```xml
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
```

А вот пример манифеста службы (указывается в предложенном выше манифесте приложения).

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
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
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы успешно развернули контейнерную службу, изучите рекомендации по управлению ее жизненным циклом в статье [Жизненный цикл приложения Service Fabric](service-fabric-application-lifecycle.md).

* [Общие сведения о Service Fabric и контейнерах](service-fabric-containers-overview.md)
* В качестве примера можно ознакомиться с [примерами кода контейнера Service Fabric на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).


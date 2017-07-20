---
title: "Service Fabric и развертывание контейнеров в Linux | Документация Майкрософт"
description: "Service Fabric и использование контейнеров Linux для развертывания приложений для микрослужб. В этой статье показаны возможности, которые предоставляет Service Fabric для контейнеров, а также описано развертывание образа контейнера Linux в кластере."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/29/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9dcec753e5f999a1bac07276373c0c25f89ec58d
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
# <a name="deploy-a-linux-container-to-service-fabric"></a>Развертывание контейнера Linux в Service Fabric
> [!div class="op_single_selector"]
> * [Развертывание контейнера Windows](service-fabric-deploy-container.md)
> * [Развертывание контейнера Linux](service-fabric-deploy-container-linux.md)
>
>

В этой статье подробно рассматривается создание контейнерных служб в контейнерах Docker в Linux.

В Service Fabric реализовано несколько способов использования контейнеров для создания приложений, состоящих из контейнерных микрослужб. Эти службы называются контейнерными.

которые включают следующие возможности:

* развертывание и активация образа контейнера;
* управление ресурсами;
* проверка подлинности репозитория;
* сопоставление порта контейнера с портом узла;
* межконтейнерное обнаружение и взаимодействие;
* возможность настройки и установки переменных среды.

## <a name="packaging-a-docker-container-with-yeoman"></a>Упаковка контейнера Docker с помощью yeoman
При упаковке контейнера в Linux у вас есть выбор — использовать шаблон yeoman или [создать пакет приложения вручную](#manually).

Приложение Service Fabric может содержать один или несколько контейнеров, каждый из которых выполняет определенную роль в работе приложения. Пакет SDK Service Fabric для Linux включает в себя генератор [Yeoman](http://yeoman.io/), который упрощает создание приложения и добавление образа контейнера. Воспользуемся Yeoman, чтобы создать приложение с одним контейнером Docker — *SimpleContainerApp*. Позже можно будет добавить дополнительные службы, изменив созданные файлы манифестов.

## <a name="install-docker-on-your-development-box"></a>Установка Docker в среде разработки

Выполните приведенные ниже команды, чтобы установить Docker в среде разработки Linux (если используется блуждающий образ в OSX, то Docker уже установлен).

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>Создание приложения
1. В окне терминала введите `yo azuresfcontainer`.
2. Укажите имя приложения, например mycontainerap.
3. Укажите URL-адрес образа контейнера из репозитория DockerHub. Параметр образа имеет вид [репозиторий]/[имя_образа].
4. Если в образе не определена точка входа рабочей нагрузки, то нужно явно указать входные команды, добавив набор команд с разделителями-запятыми, выполняемых внутри контейнера, которые обеспечат выполнение контейнера после запуска.

![Генератор Service Fabric Yeoman для контейнеров][sf-yeoman]

## <a name="deploy-the-application"></a>Развертывание приложения

### <a name="using-xplat-cli"></a>Использование кроссплатформенного интерфейса командной строки
Созданное приложение можно развернуть в локальном кластере с помощью интерфейса командной строки Azure.

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```

3. Откройте в браузере Service Fabric Explorer и перейдите по адресу http://localhost:19080/Explorer (если используется Vagrant в Mac OS X, замените localhost частным IP-адресом ВМ).
4. Разверните узел приложения. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.
5. Для удаления экземпляра приложения и отмены регистрации типа приложения используйте сценарий удаления, предоставленный в шаблоне.

    ```bash
    ./uninstall.sh
    ```

### <a name="using-azure-cli-20"></a>Использование Azure CLI 2.0

Ознакомьтесь со справочной документацией по управлению [жизненным циклом приложения с помощью Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md).

В качестве примера приложения можно ознакомиться с [примерами кода контейнера Service Fabric на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="adding-more-services-to-an-existing-application"></a>Добавление дополнительных служб в существующее приложение

Чтобы добавить службу контейнеров в приложение, созданное с использованием `yo`, выполните следующие действия:

1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfcontainer:AddService`

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

Входные команды можно передать с помощью необязательного элемента `Commands` в формате разделенного запятыми набора команд, которые нужно выполнить в самом контейнере.

> [!NOTE]
> Если в образе не определена точка входа рабочей нагрузки, то нужно явно указать входные команды в элементе `Commands`, добавив набор команд с разделителями-запятыми, выполняемых внутри контейнера, которые обеспечат выполнение контейнера после запуска.

## <a name="understand-resource-governance"></a>Концепция управления ресурсами
Управление ресурсами подразумевает, что вы можете определять ресурсы, которые контейнер может использовать на узле. Параметр `ResourceGovernancePolicy`, определяемый в манифесте приложения, позволяет объявить ограничения для ресурсов, доступных из пакета кода службы. Ограничения ресурсов можно задать для следующих ресурсов:

* Память
* MemorySwap;
* CpuShares;
* MemoryReservationInMB;  
* BlkioWeight.

> [!NOTE]
> В будущих выпусках будет добавлена поддержка ограничений на определенные параметры блочного ввода-вывода, включая число операций ввода-вывода, скорость чтения-записи и т. п.
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

## <a name="configure-container-port-to-host-port-mapping"></a>Настройка сопоставления порта контейнера с портом узла
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
С помощью политики `PortBinding` в манифесте службы можно сопоставить порт контейнера с `Endpoint`. Конечная точка `Endpoint1` может определять фиксированный порт (например, порт 80). Если не указать здесь номер порта, будет автоматически выбран случайный порт из диапазона портов для приложений кластера.

Если указать конечную точку с помощью тега `Endpoint` в манифесте службы для гостевого контейнера, Service Fabric может автоматически опубликовать эту конечную точку в службе именования. Это позволит другим службам, которые выполняются в кластере, находить этот контейнер с помощью запросов REST.

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

Зарегистрировав контейнер в службе именования, вы легко обеспечите обмен данными между контейнерами в коде своего контейнера, используя [обратный прокси-сервер](service-fabric-reverseproxy.md). Для этого достаточно указать в переменных среды http-порт прослушивания обратного прокси-сервера и имена служб, с которыми будет выполняться обмен данными. Этот процесс описан в следующем разделе.

## <a name="configure-and-set-environment-variables"></a>Настройка и установка переменных среды
Переменные среды можно указывать в манифесте служб отдельно для каждого пакета кода — как для служб, развернутых в контейнере, так и для служб, развернутых в качестве процессов или гостевых исполняемых файлов. Указанные значения переменных среды можно переопределить в манифесте приложения или указать как параметры приложения во время развертывания.

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
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

## <a name="related-articles"></a>Связанные статьи

* [Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Приступая к работе с Service Fabric и XPlat CLI](service-fabric-azure-cli.md)


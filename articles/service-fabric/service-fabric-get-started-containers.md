---
title: "Создание приложения-контейнера Azure Service Fabric | Документация Майкрософт"
description: "Создание первого приложения-контейнера в Azure Service Fabric.  Создание образа Docker с приложением, отправка образа в реестр контейнеров, сборка и развертывание приложения-контейнера Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Создание первого приложения-контейнера Service Fabric
Чтобы запустить существующее приложение в контейнере Windows кластера Service Fabric, не требуется вносить изменения в приложение. В этом кратком руководстве объясняется, как создать образ Docker, содержащий веб-приложение, отправить новый образ в реестр контейнеров Azure, создать приложение-контейнер Service Fabric и развернуть его в кластере Service Fabric.  Для работы с этой статьей необходимо знание основных понятий Docker. Чтобы ознакомиться с Docker, см. этот [обзор Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Предварительные требования
Компьютер для разработки, на котором установлено ПО, перечисленное ниже.
* Visual Studio 2015 или Visual Studio 2017.
* [Пакет SDK и средства для Service Fabric](service-fabric-get-started.md).
*  Docker для Windows.  [Скачать Docker CE для Windows (стабильная версия)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). После установки и запуска Docker щелкните правой кнопкой мыши значок в области уведомлений и выберите **Switch to Windows containers** (Переключиться на контейнеры Windows). Это необходимо для запуска образов Docker на базе Windows.

Кластер Windows минимум с тремя узлами под управлением Windows Server 2016 с контейнерами. [Создайте кластер](service-fabric-get-started-azure-cluster.md) или [используйте бесплатную ознакомительную версию Service Fabric](http://tryazureservicefabric.westus.cloudapp.azure.com/). 

Реестр контейнеров Azure. [Создайте реестр контейнеров](../container-registry/container-registry-get-started-portal.md) в своей подписке Azure. 

## <a name="create-a-simple-web-app"></a>Создание простого веб-приложения
Соберите в одном месте все ресурсы, которые нужно загрузить в образ Docker. В рамках этого краткого руководства вы создадите веб-приложение Hello World на компьютере для разработки.

1. Создайте каталог, например *c:\temp\helloworldapp*.
2. Создайте вложенный каталог *c:\temp\helloworldapp\content*.
3. Создайте файл *index.html* в каталоге *c:\temp\helloworldapp\content*.
4. Измените файл *index.html*, добавив следующую строку:
    ```
    <h1>Hello World!</h1>
    ```
5. Сохраните изменения в файле *index.html*.

## <a name="build-the-docker-image"></a>Создание образа Docker
Создайте образ на основе [образа microsft/iis](https://hub.docker.com/r/microsoft/iis/) из репозитория Docker Hub. Образ microsoft/iis является производным от базового образа операционной системы Windows Server Core и содержит службы Internet Information Services (IIS).  Если запустить этот образ в контейнере, будут автоматически запущены службы IIS и установленные веб-сайты.

Определите образ Docker в Dockerfile. Dockerfile содержит инструкции по сборке образа и загрузке приложения, которое нужно запустить. Dockerfile — это входные данные для команды ```docker build```, которая создает образ. 

1. Создайте файл *Dockerfile* (без расширения файла) в каталоге *c:\temp\helloworldapp* и добавьте следующий код:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    В этом файле Dockerfile нет команды ```ENTRYPOINT```. Она не нужна здесь. При запуске Windows Server со службами IIS процесс IIS является точкой входа, которая настроена на запуск в базовом образе.

    Дополнительные сведения см. в [справочнике по Dockerfile](https://docs.docker.com/engine/reference/builder/).

2. Выполните команду ```docker build```, чтобы создать образ, который запускает веб-приложение. Откройте окно PowerShell и перейдите в каталог *c:\temp\helloworldapp*. Выполните следующую команду:

    ```
    docker build -t helloworldapp .
    ```
    Эта команда создает новый образ согласно инструкциям в Dockerfile, и присваивает образу имя helloworldapp (-t — помечает тегом). При сборке образа извлекается базовый образ из Docker Hub и создается новый образ, который добавляет приложение поверх базового образа.  Объем [образа microsft/iis ](https://hub.docker.com/r/microsoft/iis/) и базового образа операционной системы составляет 10,5 ГБ, поэтому для их скачивания и извлечения на компьютер для разработки потребуется время.  Можете запланировать на это время обед или выпить чашку кофе.  Скачивание занимает меньше времени, если базовый образ операционной системы был ранее загружен на компьютер для разработки.

3. После выполнения команды сборки выполните команду `docker images`, чтобы получить информацию о новом образе:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Проверка работы образа в локальной среде
Проверьте работу образа локально, прежде чем отправлять его в реестр контейнеров.  

1. Запустите контейнер с помощью команды ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    Параметр *name* присваивает имя запущенному контейнеру (вместо идентификатора контейнера).

2. После запуска контейнера найдите его IP-адрес, чтобы иметь возможность подключаться к запущенному контейнеру из браузера:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Подключитесь к запущенному контейнеру.  Откройте в веб-браузере IP-адрес, возвращаемый через порт 8000, например http://172.31.194.61:8000. Вы должны увидеть заголовок Hello World! в браузере.

4. Чтобы остановить контейнер, выполните следующую команду:

    ```
    docker stop my-web-site
    ```

5. Удаление контейнера с компьютера для разработки:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Отправка образа в реестр контейнеров
Убедившись, что контейнер запускается на компьютере разработки, отправьте образ в реестр в реестре контейнеров Azure.

1. Выполните команду ``docker login``, чтобы войти в реестр контейнеров с помощью [учетных данных реестра](../container-registry/container-registry-authentication.md).

    Следующая команда передает идентификатор и пароль [субъекта-службы](../active-directory/active-directory-application-objects.md) Azure Active Directory. Например, назначение субъекта-службы для реестра позволяет автоматизировать некоторые сценарии.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. Следующая команда создает тег (псевдоним) образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда помещает образ в пространство имен ```samples```.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Отправьте образ в реестр контейнеров:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Создание и упаковка контейнерной службы в Visual Studio
Пакет SDK и средства для Service Fabric предоставляют шаблон службы для развертывания контейнера в кластере Service Fabric.

1. Запустите Visual Studio.  Выберите **Файл** > **Создать** > **Проект**.
2. Выберите **Приложение Service Fabric**, назовите его MyFirstContainer и нажмите кнопку **ОК**.
3. Выберите значение **Гостевой контейнер** из списка **шаблонов службы**.
4. В поле **Имя образа** введите myregistry.azurecr.io/samples/helloworldapp — это образ, который вы отправили в репозиторий контейнера. 
5. Присвойте службе имя и нажмите кнопку **ОК**.
6. Если контейнерной службе нужна конечная точка для обмена данными, можно добавить значения протокола, порта и типа в ```Endpoint``` файл ServiceManifest.xml. В этом кратком руководстве контейнерная служба прослушивает порт 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Если указать ```UriScheme```, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Полный пример файла ServiceManifest.xml приведен в конце этой статьи. 
7. Настройте сопоставление порта контейнера с портом узла, указав политику ```PortBinding``` в ```ContainerHostPolicies``` файле ApplicationManifest.xml.  В этом кратком руководстве ```ContainerPort``` — 8000 (контейнер предоставляет порт 8000, как указано в Dockerfile), а ```EndpointRef``` — Guest1TypeEndpoint (конечная точка, определенная в манифесте служб).  Входящие запросы к службе через порт 80 сопоставляются с портом 8000 в контейнере.  Если для контейнера требуется проверка подлинности в частном репозитории, добавьте ```RepositoryCredentials```.  Для работы с этим руководством добавьте имя учетной записи и пароль для реестра контейнеров myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Полный пример файла ApplicationManifest.xml приведен в конце этой статьи.
8. Настройте конечную точку подключения к кластеру, чтобы опубликовать приложение в кластере.  Конечную точку подключения к клиенту можно найти в колонке "Обзор" кластера на [портале Azure](https://portal.azure.com). В обозревателе решений в меню **MyFirstContainer**->**PublishProfiles** откройте файл *Cloud.xml*.  Добавьте имя кластера и порт подключения в раздел **ClusterConnectionParameters**.  Например:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Сохраните все файлы и выполните сборку проекта.  

10. Чтобы упаковать приложение, щелкните правой кнопкой мыши **MyFirstContainer** в обозревателе решений и выберите действие **Упаковать**. 

## <a name="deploy-the-container-app"></a>Развертывание приложения-контейнера
1. Чтобы опубликовать приложение, щелкните правой кнопкой мыши **MyFirstContainer** в обозревателе решений и выберите действие **Опубликовать**.

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) — это веб-средство для проверки приложений и узлов в кластере Service Fabric и управления ими. Откройте браузер, перейдите по адресу http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ и выполните развертывание приложения.  Приложение развертывается, но находится в состоянии ошибки, пока на узлы кластера не будет загружен образ (что может занять некоторое время в зависимости от размера образа):  ![Ошибка][1]

3. Приложение будет готово, когда оно перейдет в состояние ```Ready```:  ![Готово][2]

4. Откройте браузер и перейдите по адресу http://containercluster.westus2.cloudapp.azure.com. Вы должны увидеть заголовок Hello World! в браузере.

## <a name="clean-up"></a>Очистка
Пока кластер работает, с вас будет взиматься плата. Рекомендуем [удалить кластер](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  [Сторонние кластеры](http://tryazureservicefabric.westus.cloudapp.azure.com/) удаляются автоматически через несколько часов.

После передачи образа в реестр контейнеров можно удалить локальный образ с компьютера для разработки:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Полные примеры манифестов службы и приложения Service Fabric
Ниже приведены полные коды манифестов службы и приложения, используемых в этом кратком руководстве.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-containers-overview.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* Извлечение [примеров кода контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) на GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png


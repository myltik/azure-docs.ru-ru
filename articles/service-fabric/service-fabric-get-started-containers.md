---
title: "Создание приложения-контейнера Azure Service Fabric | Документация Майкрософт"
description: "Создание первого приложения-контейнера Windows в Azure Service Fabric.  Создание образа Docker с приложением Python, отправка образа в реестр контейнеров, сборка и развертывание приложения-контейнера Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 8c9d6c65666b5ffedf058e0a83d4fc41fff80235
ms.contentlocale: ru-ru
ms.lasthandoff: 07/15/2017

---

# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Создание первого контейнера-приложения Service Fabric в Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Чтобы запустить существующее приложение в контейнере Windows кластера Service Fabric, не требуется вносить изменения в приложение. В этой статье описано создание образа Docker, содержащего веб-приложение [Flask](http://flask.pocoo.org/) Python, и его развертывание в кластер Service Fabric.  Кроме того, вы предоставите общий доступ к контейнерному приложению через [реестр контейнеров Azure](/azure/container-registry/).  Для работы с этой статьей необходимо знание основных понятий Docker. Чтобы ознакомиться с Docker, см. этот [обзор Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Предварительные требования
Компьютер для разработки, на котором установлено ПО, перечисленное ниже.
* Visual Studio 2015 или Visual Studio 2017.
* [Пакет SDK и средства для Service Fabric](service-fabric-get-started.md).
*  Docker для Windows.  [Скачать Docker CE для Windows (стабильная версия)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). После установки и запуска Docker щелкните правой кнопкой мыши значок в области уведомлений и выберите **Switch to Windows containers** (Переключиться на контейнеры Windows). Это необходимо для запуска образов Docker на базе Windows.

Кластер Windows минимум с тремя узлами под управлением Windows Server 2016 с контейнерами. [Создайте кластер](service-fabric-cluster-creation-via-portal.md) или [используйте бесплатную ознакомительную версию Service Fabric](https://aka.ms/tryservicefabric). 

Реестр контейнеров Azure. [Создайте реестр контейнеров](../container-registry/container-registry-get-started-portal.md) в своей подписке Azure. 

## <a name="define-the-docker-container"></a>Определение образа контейнера Docker
Создайте образ на основе [образа Python](https://hub.docker.com/_/python/) из репозитория Docker Hub. 

Определите образ Docker в файле Dockerfile. Файл Dockerfile содержит инструкции по настройке среды внутри контейнера, загрузке приложения, которое необходимо выполнить, и сопоставлению портов. Dockerfile — это входные данные для команды `docker build`, которая создает образ. 

Создайте пустой каталог и файл *Dockerfile* (без расширения файла). Добавьте следующий код в *Dockerfile* и сохраните изменения:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Дополнительные сведения см. в [справочнике по Dockerfile](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-simple-web-application"></a>Создание простого веб-приложения
Создайте веб-приложение Flask, ожидающее передачи данных в порт 80, возвращающее строку Hello, World!.  В том же каталоге создайте файл *requirements.txt*.  Добавьте следующее и сохраните изменения:
```
Flask
```

Кроме того, создайте файл *app.py* и добавьте следующее:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Создание образа
Выполните команду `docker build`, чтобы создать образ, который запускает веб-приложение. Откройте окно PowerShell и перейдите в каталог, содержащий Dockerfile. Выполните следующую команду:

```
docker build -t helloworldapp .
```

Эта команда создает новый образ согласно инструкциям в Dockerfile, и присваивает образу имя helloworldapp (-t — помечает тегом). При сборке образа извлекается базовый образ из Docker Hub и создается новый образ, который добавляет приложение поверх базового образа.  

После выполнения команды сборки выполните команду `docker images`, чтобы получить информацию о новом образе:

```
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Локальный запуск приложения
Проверьте работу образа локально, прежде чем отправлять его в реестр контейнеров.  

Запустите приложение:

```
docker run -d --name my-web-site helloworldapp
```

Параметр *name* присваивает имя запущенному контейнеру (вместо идентификатора контейнера).

После запуска контейнера найдите его IP-адрес, чтобы иметь возможность подключаться к запущенному контейнеру из браузера:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Подключитесь к запущенному контейнеру.  Откройте в веб-браузере возвращаемый IP-адрес, например http://172.31.194.61. Вы должны увидеть заголовок Hello World! в браузере.

Чтобы остановить контейнер, выполните следующую команду:

```
docker stop my-web-site
```

Удаление контейнера с компьютера для разработки:

```
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Отправка образа в реестр контейнеров
Убедившись, что контейнер запускается на компьютере разработки, отправьте образ в реестр в реестре контейнеров Azure.

Выполните команду ``docker login``, чтобы войти в реестр контейнеров с помощью [учетных данных реестра](../container-registry/container-registry-authentication.md).

Следующая команда передает идентификатор и пароль [субъекта-службы](../active-directory/active-directory-application-objects.md) Azure Active Directory. Например, назначение субъекта-службы для реестра позволяет автоматизировать некоторые сценарии. Или вы могли бы входить, используя имя и пароль реестра.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Следующая команда создает тег (псевдоним) образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда помещает образ в пространство имен ```samples```.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Отправьте образ в реестр контейнеров:

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
6. Если контейнерной службе нужна конечная точка для обмена данными, можно добавить значения протокола, порта и типа в ```Endpoint``` файл ServiceManifest.xml. В этой статье контейнерная служба ожидает передачи данных порта 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    ```
    Если указать ```UriScheme```, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Полный пример файла ServiceManifest.xml приведен в конце этой статьи. 
7. Настройте сопоставление порта контейнера с портом узла, указав политику ```PortBinding``` в ```ContainerHostPolicies``` файле ApplicationManifest.xml.  В этой статьей ```ContainerPort``` — 8081 (контейнер предоставляет порт 80, как указано в Dockerfile), а ```EndpointRef``` — Guest1TypeEndpoint (конечная точка, определенная в манифесте служб).  Входящие запросы к службе через порт 8081 сопоставляются с портом 80 в контейнере.  Если для контейнера требуется проверка подлинности в частном репозитории, добавьте ```RepositoryCredentials```.  Для работы с этим руководством добавьте имя учетной записи и пароль для реестра контейнеров myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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

## <a name="deploy-the-container-application"></a>Развертывание приложения-контейнера
Чтобы опубликовать приложение, щелкните правой кнопкой мыши **MyFirstContainer** в обозревателе решений и выберите действие **Опубликовать**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) — это веб-средство для проверки приложений и узлов в кластере Service Fabric и управления ими. Откройте браузер, перейдите по адресу http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ и выполните развертывание приложения.  Приложение развертывается, но находится в состоянии ошибки, пока на узлы кластера не будет загружен образ (что может занять некоторое время в зависимости от размера образа): ![Ошибка][1]

Приложение будет готово, когда оно перейдет в состояние ```Ready```: ![Готово][2]

Откройте браузер и перейдите по адресу http://containercluster.westus2.cloudapp.azure.com:8081. Вы должны увидеть заголовок Hello World! в браузере.

## <a name="clean-up"></a>Очистка
Пока кластер работает, с вас будет взиматься плата. Рекомендуем [удалить кластер](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  [Сторонние кластеры](http://tryazureservicefabric.westus.cloudapp.azure.com/) удаляются автоматически через несколько часов.

После передачи образа в реестр контейнеров можно удалить локальный образ с компьютера для разработки:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Полные примеры манифестов службы и приложения Service Fabric
Ниже приведены полные коды манифестов службы и приложения, используемых в этой статье.

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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
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
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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
* Ознакомьтесь с руководством [Развертывание приложения-контейнера .NET в Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* Извлечение [примеров кода контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) на GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png


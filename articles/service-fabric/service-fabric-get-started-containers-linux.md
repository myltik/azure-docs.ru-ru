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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 35b7e0a730d73f646462b9cde3c8bbabac4d7c67
ms.contentlocale: ru-ru
ms.lasthandoff: 08/16/2017

---

# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Создание первого контейнера-приложения Service Fabric в Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Чтобы запустить существующее приложение в контейнере Linux кластера Service Fabric не требуется вносить изменения в приложение. В этой статье описано создание образа Docker, содержащего веб-приложение [Flask](http://flask.pocoo.org/) Python, и его развертывание в кластер Service Fabric.  Кроме того, вы предоставите общий доступ к контейнерному приложению через [реестр контейнеров Azure](/azure/container-registry/).  Для работы с этой статьей необходимо знание основных понятий Docker. Чтобы ознакомиться с Docker, см. этот [обзор Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Предварительные требования
* Компьютер для разработки, на котором установлено ПО, перечисленное ниже.
  * [Пакет SDK и средства для Service Fabric](service-fabric-get-started-linux.md).
  * [Предварительные выпуски](https://docs.docker.com/engine/installation/#prior-releases). 

* Реестр контейнеров Azure. [Создайте реестр контейнеров](../container-registry/container-registry-get-started-portal.md) в своей подписке Azure. 

## <a name="define-the-docker-container"></a>Определение образа контейнера Docker
Создайте образ на основе [образа Python](https://hub.docker.com/_/python/) из репозитория Docker Hub. 

Определите образ Docker в файле Dockerfile. Файл Dockerfile содержит инструкции по настройке среды внутри контейнера, загрузке приложения, которое необходимо выполнить, и сопоставлению портов. Dockerfile — это входные данные для команды `docker build`, которая создает образ. 

Создайте пустой каталог и файл *Dockerfile* (без расширения файла). Добавьте следующий код в *Dockerfile* и сохраните изменения:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
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
Выполните команду `docker build`, чтобы создать образ, который запускает веб-приложение. Откройте окно PowerShell и перейдите в каталог *c:\temp\helloworldapp*. Выполните следующую команду:

```bash
docker build -t helloworldapp .
```

Эта команда создает новый образ согласно инструкциям в Dockerfile, и присваивает образу имя helloworldapp (-t — помечает тегом). При сборке образа извлекается базовый образ из Docker Hub и создается новый образ, который добавляет приложение поверх базового образа.  

После выполнения команды сборки выполните команду `docker images`, чтобы получить информацию о новом образе:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Локальный запуск приложения
Убедитесь, что контейнерное приложение выполняется локально перед принудительной отправкой реестр контейнеров.  

Запустите приложение, сопоставив порт 4000 компьютера с предоставленным портом 80 контейнера:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

Параметр *name* присваивает имя запущенному контейнеру (вместо идентификатора контейнера).

Подключитесь к запущенному контейнеру.  Откройте в веб-браузере IP-адрес, возвращаемый через порт 4000, например http://localhost:4000. Вы должны увидеть заголовок Hello World! в браузере.

![Привет, мир!][hello-world]

Чтобы остановить контейнер, выполните следующую команду:

```bash
docker stop my-web-site
```

Удаление контейнера с компьютера для разработки:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Отправка образа в реестр контейнеров
Убедившись, что приложение запускается в Docker, отправьте образ в реестр в реестре контейнеров Azure.

Выполните команду `docker login`, чтобы войти в реестр контейнеров с помощью [учетных данных реестра](../container-registry/container-registry-authentication.md).

Следующая команда передает идентификатор и пароль [субъекта-службы](../active-directory/active-directory-application-objects.md) Azure Active Directory. Например, назначение субъекта-службы для реестра позволяет автоматизировать некоторые сценарии.  Или вы могли бы входить, используя имя и пароль реестра.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Следующая команда создает тег (псевдоним) образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда помещает образ в пространство имен `samples`.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Отправьте образ в реестр контейнеров:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Упаковка образа Docker с помощью Yeoman
Пакет SDK Service Fabric для Linux включает в себя генератор [Yeoman](http://yeoman.io/), который упрощает создание приложения и добавление образа контейнера. Воспользуемся Yeoman, чтобы создать приложение с одним контейнером Docker — *SimpleContainerApp*.

Чтобы создать приложение-контейнер Service Fabric, откройте окно терминала и выполните `yo azuresfcontainer`.  

Укажите имя приложения, например mycontainerap. 

Предоставьте URL-адрес для образа контейнеров в реестре контейнеров. 

В образе определена точка входа рабочей нагрузки, поэтому нужно явно указать входные команды, выполняемые внутри контейнера, которые обеспечат выполнение контейнера после запуска. 

Укажите число экземпляров — 1.

![Генератор Service Fabric Yeoman для контейнеров][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Настройка сопоставления порта и проверки подлинности репозитория контейнера
Контейнерной службе необходима конечная точка для взаимодействия.  Теперь добавьте протокол, порт и тип для `Endpoint` в файле ServiceManifest.xml. В этой статье контейнерная служба ожидает передачи данных порта 4000: 

```xml
<Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
```
Если указать `UriScheme`, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Полный пример файла ServiceManifest.xml приведен в конце этой статьи. 

Настройте сопоставление порта контейнера с портом узла, указав политику `PortBinding` в `ContainerHostPolicies` файле ApplicationManifest.xml.  В этой статье `ContainerPort` — 80 (контейнер предоставляет порт 80, как указано в Dockerfile), а `EndpointRef` — myserviceTypeEndpoint (конечная точка, определенная в манифесте служб).  Входящие запросы к службе через порт 4000 сопоставляются с портом 80 в контейнере.  Если для контейнера требуется проверка подлинности в частном репозитории, добавьте `RepositoryCredentials`.  Для работы с этим руководством добавьте имя учетной записи и пароль для реестра контейнеров myregistry.azurecr.io. 

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Создание и упаковка приложений Service Fabric
Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала. Используйте следующую команду, чтобы собрать и упаковать приложение:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Развертывание приложения
Созданное приложение можно развернуть в локальном кластере с помощью интерфейса командной строки Azure.

Подключитесь к удаленному кластеру Service Fabric.

```bash
azure servicefabric cluster connect
```

С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

```bash
./install.sh
```

Откройте в браузере Service Fabric Explorer и перейдите по адресу http://localhost:19080/Explorer (если используется Vagrant в Mac OS X, замените localhost частным IP-адресом ВМ). Разверните узел приложения. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

Подключитесь к запущенному контейнеру.  Откройте в веб-браузере IP-адрес, возвращаемый через порт 4000, например http://localhost:4000. Вы должны увидеть заголовок Hello World! в браузере.

![Привет, мир!][hello-world]

## <a name="clean-up"></a>Очистка
Для удаления экземпляра приложения из локального кластера разработки и отмены регистрации типа приложения используйте сценарий удаления, предоставленный в шаблоне.

```bash
./uninstall.sh
```

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
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Добавление дополнительных служб в существующее приложение

Чтобы добавить службу контейнеров в приложение, созданное с использованием yeoman, выполните следующие действия:

1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Настройка интервала времени перед принудительным завершением контейнера

Вы можете настроить интервал времени для среды выполнения перед удалением контейнера после начала удаления службы (или перехода на другой узел). При настройке интервала времени в контейнер отправляется команда `docker stop <time in seconds>`.   Дополнительные сведения см. в статье [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) (Остановка docker). Интервал времени ожидания указывается в разделе `Hosting`. В следующем фрагменте манифеста кластера показано, как задать интервал ожидания:

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
Интервал времени по умолчанию установлен на 10 секунд. Так как эта конфигурация является динамической, файл конфигурации обновляет только кластер для которого обновляется время ожидания. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Настройка среды выполнения для удаления неиспользуемых образов контейнера

Можно настроить кластер Service Fabric для удаления неиспользуемых образов контейнера из узла. Эта конфигурация позволяет месту на диске перезаписываться, если на узле находится слишком много образов контейнера.  Чтобы включить эту функцию, обновите раздел `Hosting` в манифесте кластера, как показано в следующем фрагменте кода: 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Образы, которые не должны удаляться, можно указать в параметре `ContainerImagesToSkip`. 


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-containers-overview.md).
* Ознакомьтесь с руководством [Развертывание приложения-контейнера .NET в Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
* Извлечение [примеров кода контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) на GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png


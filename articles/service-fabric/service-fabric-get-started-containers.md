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
ms.date: 11/03/2017
ms.author: ryanwi
ms.openlocfilehash: 3d58ba0985d7a5bb302028254be0951859b79dbb
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
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

<a id="Build-Containers"></a>
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

<a id="Push-Containers"></a>
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

## <a name="create-the-containerized-service-in-visual-studio"></a>Создание контейнерной службы в Visual Studio
Пакет SDK и средства для Service Fabric предоставляют шаблон службы для создания контейнерного приложения.

1. Запустите Visual Studio.  Выберите **Файл** > **Создать** > **Проект**.
2. Выберите **Приложение Service Fabric**, назовите его MyFirstContainer и нажмите кнопку **ОК**.
3. В списке **шаблонов служб** выберите значение **Container** (Контейнер).
4. В поле **Имя образа** введите myregistry.azurecr.io/samples/helloworldapp — это образ, который вы отправили в репозиторий контейнера.
5. Присвойте службе имя и нажмите кнопку **ОК**.

## <a name="configure-communication"></a>Настройка обмена данными
Контейнерной службе для обмена данными требуется конечная точка. В файле ServiceManifest.xml добавьте элемент `Endpoint` и укажите протокол, порт и тип. В этой статье контейнерная служба ожидает передачи данных на порту 8081.  В этом примере используется фиксированный порт 8081.  Если порт не указан, выбирается случайный порт из диапазона портов приложения.  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

При определении конечной точки Service Fabric публикует ее в службе именования.  Другие службы, работающие в кластере, могут разрешать этот контейнер.  Кроме того, возможен обмен данными между контейнерами с помощью [обратного прокси-сервера](service-fabric-reverseproxy.md).  Для этого нужно указать в переменных среды HTTP-порт прослушивания обратного прокси-сервера и имена служб, с которыми будет выполняться обмен данными.

## <a name="configure-and-set-environment-variables"></a>Настройка и установка переменных среды
Переменные среды можно указать для каждого пакета кода в манифесте служб. Эта функция доступна для всех служб, вне зависимости от того, как они развернуты: в качестве контейнеров, процессов или гостевых исполняемых файлов. Вы можете переопределить значения переменных среды в манифесте приложения или указать их в качестве параметров приложения во время развертывания.

Следующий фрагмент XML-кода из манифеста службы демонстрирует, как определить переменные среды для пакета кода.
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Эти переменные среды можно переопределить в манифесте приложения:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Настройка сопоставления порта контейнера с портом узла и межконтейнерного обнаружения
Настройте порт узла, который будет использоваться для обмена данными с контейнером. Привязка порта сопоставляет порт, на котором служба ожидает передачи данных в контейнере, с портом на узле. Добавьте элемент `PortBinding` в элемент `ContainerHostPolicies` в файле ApplicationManifest.xml.  В этой статье у `ContainerPort` будет значение 80 (контейнер предоставляет порт 80, как указано в Dockerfile), а у `EndpointRef` — Guest1TypeEndpoint (конечная точка, определенная ранее в манифесте служб).  Входящие запросы к службе через порт 8081 сопоставляются с портом 80 в контейнере.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>Настройка проверки подлинности в реестре контейнеров
Чтобы настроить проверку подлинности для реестра контейнеров, в файле ApplicationManifest.xml добавьте `RepositoryCredentials` в элемент `ContainerHostPolicies`. Добавьте учетную запись и пароль для реестра контейнеров myregistry.azurecr.io. Это позволит службе скачать образ контейнера из репозитория.

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

Рекомендуем зашифровать пароль репозитория с помощью сертификата шифрования, который развертывается на всех узлах кластера. Когда служба Service Fabric развернет пакет службы в кластере, сертификат шифрования будет использоваться для расшифровки зашифрованного текста.  Командлет Invoke-ServiceFabricEncryptText используется для создания зашифрованного текста пароля, который добавляется в файл ApplicationManifest.xml.

Указанный ниже скрипт создает новый самозаверяющий сертификат и экспортирует его в PFX-файл.  Этот сертификат импортируется в существующее хранилище ключей и затем развертывается в кластере Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Зашифруйте пароль с помощью командлета [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Замените пароль зашифрованным текстом, полученным с помощью командлета [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps), и задайте для параметра `PasswordEncrypted` значение true.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>Настройка режима изоляции
Windows поддерживает два режима изоляции для контейнеров: режим изоляции процессов и Hyper-V. В режиме изоляции процесса все контейнеры, запущенные на одном хост-компьютере, совместно используют ядро и узел. В режиме изоляции Hyper-V все контейнеры Hyper-V и узлы контейнера используют отдельные ядра. Режим изоляции указывается в элементе `ContainerHostPolicies` в файле манифеста приложения. Вы можете указать следующие режимы изоляции: `process`, `hyperv` и `default`. Режим изоляции на узлах Windows Server по умолчанию имеет значение `process`, а на узлах Windows 10 — значение `hyperv`. В указанном ниже фрагменте кода показано, как режим изоляции указывается в файле манифеста приложения.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Режим изоляции Hyper-v доступен для номеров SKU Azure Ev3 и Dv3 с поддержкой вложенной виртуализации. 
   >
   >

## <a name="configure-resource-governance"></a>Настройка управления ресурсами
[Управление ресурсами](service-fabric-resource-governance.md) позволяет ограничить ресурсы, которые контейнер может использовать на узле. Элемент `ResourceGovernancePolicy`, определяемый в манифесте приложения, позволяет объявить ограничения для ресурсов, доступных из пакета кода службы. Ограничения можно задать для следующих ресурсов: Memory, MemorySwap, CpuShares (относительный вес ЦП), MemoryReservationInMB, BlkioWeight (относительный вес BlockIO).  В этом примере пакет службы Guest1Pkg получает одно ядро на узлах кластера, где он размещается.  Ограничения по памяти абсолютны, так что пакет кода получает только 1024 МБ (и имеет соответствующие мягкие гарантии резервирования). Пакеты кода (контейнеры или процессы) не могут выделить больше памяти, чем задано ограничением, а при попытке сделать это возникнет проблема нехватки памяти. Чтобы принудительное ограничение ресурсов работало, для всех пакетов кода в пакете службы должны быть указаны ограничения по памяти.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```

## <a name="deploy-the-container-application"></a>Развертывание приложения-контейнера
Сохраните все изменения и скомпилируйте приложение. Чтобы опубликовать приложение, щелкните правой кнопкой мыши **MyFirstContainer** в обозревателе решений и выберите действие **Опубликовать**.

В поле **Конечная точка подключения** введите конечную точку управления для кластера.  Например, containercluster.westus2.cloudapp.azure.com:19000. Конечную точку подключения к клиенту можно найти в колонке "Обзор" кластера на [портале Azure](https://portal.azure.com).

Щелкните **Опубликовать**.

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
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

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
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
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
* Извлечение [примеров кода контейнера Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) на GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

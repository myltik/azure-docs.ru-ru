---
title: Развертывание приложения Node.js с помощью MongoDB | Microsoft Docs
description: Пошаговое руководство по упаковке нескольких пользовательских приложений для развертывания в кластере Service Fabric в Azure
services: service-fabric
documentationcenter: .net
author: bmscholl
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2016
ms.author: bscholl;mikhegn

---
# Развертывание нескольких пользовательских приложений
В этой статье показано, как упаковать и развернуть несколько пользовательских приложений в Azure Service Fabric с помощью предварительной версии средства упаковки Service Fabric, которое доступно по ссылке [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

Для сборки пакета Service Fabric вручную прочтите статью [Развертывание настраиваемого приложения в Service Fabric](service-fabric-deploy-existing-app.md).

Хотя в этом пошаговом руководстве показано, как развернуть приложение с клиентом Node.js и MongoDB в качестве хранилища данных, эти действия можно применить к любому приложению, которое зависит от другого приложения.

## Упаковка приложения Node.js
В этой статье предполагается, что Node.js не установлен на узлах в кластере Service Fabric. Следовательно, необходимо добавить Node.exe в корневой каталог приложения узла перед упаковкой. Структура каталогов приложения Node.js (использующего веб-платформу Express и подсистему шаблонов Jade) должна быть аналогична приведенной ниже.

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

На следующем шаге вы создадите пакет приложения для приложения Node.js. Приведенный ниже код создает пакет приложения Service Fabric, содержащий приложение Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Ниже приведено описание используемых параметров.

* **/source**: указывает на каталог приложения, которое должно быть упаковано.
* **/target**: определяет каталог, в котором должен быть создан пакет. Этот каталог должен отличаться от исходного.
* **/appname**: определяет имя приложения для существующего приложения. Важно понимать, что это имя преобразуется в имя службы в манифесте, а не в имя приложения Service Fabric.
* **/exe**: определяет исполняемый файл, который должен запустить Service Fabric, в данном случае `node.exe`.
* **/ma**: определяет аргумент, который используется для запуска исполняемого файла. Так как Node.js не установлен, Service Fabric необходимо запустить веб-сервер Node.js, выполнив команду `node.exe bin/www`. `/ma:'bin/www'` сообщает средству упаковки о том, что необходимо использовать `bin/ma` в качестве аргумента для node.exe.
* **/AppType**: определяет имя типа приложения Service Fabric.

> [!NOTE]
> Для создания пакета приложения в составе проекта приложения можно также использовать Visual Studio. Если связать источник с проектом Visual Studio, при построении решения Visual Studio следует убедиться, что в пакете приложения отображаются последние изменения в источнике. [Упаковка имеющегося приложения с использованием Visual Studio](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)
> 
> 

Если перейти в каталог, указанный в параметре/target, вы увидите, что средство создания пакетов сформировало полноценный пакет Service Fabric, как показано ниже.

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
В созданном файле ServiceManifest.xml теперь имеется раздел, описывающий, как нужно запускать веб-сервер Node.js, как показано в следующем фрагменте кода.

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
В этом примере веб-сервер Node.js прослушивает порт 3000, поэтому необходимо обновить сведения о конечной точке в файле ServiceManifest.xml, как показано ниже.

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
Теперь, когда у вас есть упакованное приложение Node.js, вы можете создать пакет для MongoDB. Как упоминалось ранее, действия, которые вы выполняете, не являются специфическими для Node.js и MongoDB. На самом деле, их можно применить ко всем приложениям, которые нужно упаковать в одно приложение Service Fabric.

Для упаковки MongoDB нужно убедиться, что вы упаковали файлы mongod.exe и mongo.exe. Оба двоичных файла находятся в подкаталоге `bin` в каталоге установке MongoDB. Структура каталога похожа на показанную ниже.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric должна запустить MongoDB с помощью команды, аналогичной показанной ниже, поэтому необходимо использовать параметр `/ma` при упаковке MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> В случае сбоя узла, если вы поместили каталог данных MongoDB в локальный каталог узла, данные не сохраняются. Следует использовать устойчивое хранилище или реализовать набор реплик MongoDB, чтобы предотвратить потерю данных.
> 
> 

В PowerShell или командной строке средство упаковки запускается со следующими параметрами:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Для добавления MongoDB в пакет приложения Service Fabric необходимо убедиться, что параметр /target указывает на тот же каталог, который уже содержит манифест приложения вместе с приложением Node.js. Кроме того, необходимо убедиться, что вы используете тот же тип ApplicationType.

> [!NOTE]
> Для создания пакета приложения в составе проекта приложения можно также использовать Visual Studio. Если связать источник с проектом Visual Studio, при построении решения Visual Studio следует убедиться, что в пакете приложения отображаются последние изменения в источнике. [Упаковка имеющегося приложения с использованием Visual Studio](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)
> 
> 

Давайте перейдем в каталог и посмотрим, что получилось.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Как видите, средство добавило новый каталог MongoDB в каталог, содержащий двоичные файлы MongoDB. Если открыть файл `ApplicationManifest.xml`, вы увидите, что пакет теперь содержит как приложение Node.js, так и MongoDB. В следующем примере кода показано содержимое манифеста приложения.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

Последним шагом является публикация приложения в локальный кластер Service Fabric с помощью сценариев PowerShell, приведенных ниже.

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

> [!NOTE]
> С помощью Visual Studio можно опубликовать приложение локально, нажав клавишу F5 для отладки или используя мастер публикации.
> 
> 

После успешной публикации приложения в локальный кластер вы можете обратиться к приложению Node.js, используя номер порта, который мы ввели в манифесте службы приложения Node.js, например http://localhost:3000.

Из этого учебника вы узнали, как легко упаковать два существующих приложения в одно приложение Service Fabric. Кроме того, вы узнали, как развернуть его в Service Fabric, так чтобы воспользоваться преимуществами некоторых функций Service Fabric, таких как высокая доступность и система контроля работоспособности.

## Дальнейшие действия
* Узнайте, как [вручную упаковать гостевое приложение](service-fabric-deploy-existing-app.md).

<!---HONumber=AcomDC_0622_2016-->
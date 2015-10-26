<properties
   pageTitle="Node.js и надежные субъекты | Microsoft Azure"
   description="Описание процедуры создания приложения Express node.js, использующего надежные субъекты и выполняемого на платформе Azure Service Fabric."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js и надежные субъекты: выигрышное сочетание
Эта статья содержит обзор способа создания приложения, в котором используются node.js и надежные субъекты. Конечное решение представляет собой сочетание кода javascript, используемого в основном для обеспечения работы внешней части приложения (Rest и веб-API), и C# для более сложных вычислений. Использование модели программирования Service Fabric сразу делает приложение масштабируемым и надежным. Весь процесс основан на следующих действиях:

1. Создание нового проекта субъекта без отслеживания состояния или с ним в Service Fabric с использованием инструментов Service Fabric для Visual Studio.
2. Создание проекта node.js с использованием, к примеру, [инструментов node.js для Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC). 
3. Добавление проекта node.js (например, базового приложения Express 4) в решение Service Fabric. Вы можете воспользоваться функцией добавления существующего проекта, чтобы включить проект node.js. 
4. Упаковка приложения node.js для его последующего развертывания с помощью инструментов Visual Studio для Service Fabric.

## Создание проекта node.js
После создания проекта node.js и добавления зависимостей вам потребуется изменить структуру каталогов проекта, чтобы она соответствовала структуре, необходимой инструментам Service Fabric для Visual Studio для упаковки и развертывания приложения подобно любой другой службе Service Fabric. Цель — внести изменения в структуру каталогов лишь один раз, чтобы вы могли воспользоваться процессом развертывания в Visual Studio и развернуть приложение node.js вместе с другими службами в решении. В структуру каталогов требуется внести следующие изменения:

1. создать каталог PackageRoot;
2. создать каталог Code в каталоге PackageRoot;
3. переместить все файлы и каталоги в каталог Code.

После выполнения этих действий структура проекта в Visual Studio должна выглядеть следующим образом:

![][8]

Как видите, весь код node.js находится в каталоге PackageRoot/Code. Платформа Service Fabric не делает каких-либо предположений относительно приложений и библиотек, установленных на узле, где будет развернуто приложение, поэтому вам потребуется включить все зависимости. В случае с node.js вам потребуется добавить файл node.exe, чтобы платформа Service Fabric могла выполнить код (файл node.exe находится в каталоге program files\\nodejs).

![][3]

## Добавление файла метаданных servicemanifest.xml
Чтобы развернуть приложение node.js, нам потребуется добавить файл метаданных, необходимый для указания некоторых свойств, которые Service Fabric будет использовать, чтобы определить метод развертывания и запуска приложения.

Ниже приведен пример того, как должен выглядеть файл servicemanifest.xml. Дополнительные сведения о важных элементах, которые необходимо обновить, можно найти в [этой статье](service-fabric-deploy-existing-app.md), но обычно обновить требуется следующие элементы:

* Name (элемент ServiceManifest)
* ServiceTypeName (элемент StatelessServiceType)
* Arguments (элемент ExeHost). Требуется, чтобы указать файл js, которые следует использовать для запуска приложения.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Обратите внимание, что один из параметров файла servicemanifest.xml может не поддерживаться node.js. В некоторых случаях параметр `ConsoleRedirection` не работает, если модуль node.js (например, Express) полагает, что файловые дескрипторы (fd) для потоков "stdout" и "stderr" — 1 и 2 соответственно.

После добавления файла `servicemanifest.xml` в проект node.js структура проекта должна выглядеть следующим образом:

![][4]

## Добавление двоичных файлов Service Fabric
Далее необходимо добавить двоичные файлы Service Fabric, используемые для подключения к субъектам, выполняемым в кластере Service Fabric. Как видно в примере Edge.js, код содержит ссылки на сборки. Чтобы открыть edge.js доступ к этим сборкам, их нужно скопировать вместе с кодом node.js. Проще всего скопировать двоичные файлы из существующих проектов. В каталог с кодом требуется скопировать следующие файлы (их будет использовать edge.js):

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

После добавления двоичных файлов в проект его структура должна выглядеть следующим образом:

![][5]

## Добавление ссылки на проект node.js в файле applicationmanifest
Далее необходимо добавить службу node.js в манифест приложения, чтобы его можно было развернуть с помощью инструментов Visual Studio для Service Fabric. Это необходимо, поскольку интеграция инструментов Service Fabric для Visual Studio с проектом node.js отсутствует, поэтому его требуется добавить вручную

В файле `applicationmanifest.xml` добавьте следующие элементы:

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Убедитесь, что вы используете те же имена, что и в `servicemanifest.xml` для указания `ServiceName` и `ServiceTypeName`. Файл `applicationmanifest.xml` должен выглядеть следующим образом:

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Использование надежных субъектов в приложении Node.js
Теперь структура проекта задана, и можно начать работу с кодом, позволяющим приложению node.js подключаться к надежным субъектам в кластере, как и при использовании приложения .NET. На этом этапе требуется создать приложение node.js, выполняющее роль интерфейса или шлюза для клиентского приложения и предоставить веб-приложение или набор API REST, которые может использовать клиентское приложение. Node.js предоставляет интерфейсную часть приложения, а надежные субъекты — масштабируемый и надежный уровень "сервера приложений" приложения. В Service Fabric надежные субъекты можно вызвать с помощью [класса ActorProxy](service-fabric-reliable-actors-introduction.md#actor-communication). К счастью, существует отличный модуль node.js, который можно использовать для вызова кода .NET: [Edge.js](https://github.com/tjanczuk/edge). Вы можете использовать инструкции в репозитории GitHub, чтобы узнать особенности установки Edge на компьютер.

 
![][2]

После установки Edge с помощью NPM или пользовательского интерфейса NPM в Visual Studio вам потребуется переместить установленный модуль в каталог node\_modules в подкаталоге Code (мы изменили структуру проекта node.js и переместили весь код в каталог PackageRoot/Code). В репозитории edje.js на GitHub можно найти хорошие примеры использования Edge для вызова кода .NET. Ниже приведен простой пример кода, позволяющего вызывать надежные субъекты с помощью класса ActorProxy.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]Класс ActorProxy использует интерфейс субъекта, чтобы определить, к какому субъекту необходимо подключиться. Чтобы управляемый код, выполняемый в процессе node.js, создать экземпляр этого класса, сборку интерфейса субъекта требуется скопировать в каталог с кодом, подобно остальным DLL-файлам Service Fabric. Примечание. Вам потребуется скопировать (или задать действие, выполняемое после построения) DLL-файл при каждом изменении методов или параметров в интерфейсе.

![][6]

## Развертывание
На этом этапе проект можно развернуть с помощью инструментов Service Fabric для Visual Studio. Последний этап процесса — добавить ссылку на этот проект в проекте приложения Service Fabric, чтобы его можно было включить в пакет приложения и развернуть в кластере.

![][9]
 
Вы можете развернуть приложение (включающее приложение node.js) с помощью, например, контекстного меню решения.

![][10]

## Дальнейшие действия
* Дополнительные сведения о [надежных субъектах](service-fabric-reliable-actors-introduction.md).
* Дополнительные сведения о [жизненном цикле приложений](service-fabric-application-lifecycle.md) Service Fabric.
* Дополнительные сведения об обновлении [приложения Service Fabric](service-fabric-application-upgrade.md). 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Oct15_HO3-->
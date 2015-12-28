<properties
   pageTitle="Развертывание приложения Service Fabric | Microsoft Azure"
   description="Развертывание и удаление приложений в Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/10/2015"
   ms.author="seanmck"/>

# Развертывание приложения

После [создания пакета приложения][10] его можно развернуть в кластере Azure Service Fabric. Развертывание включает следующие три шага:

1. загрузка пакета приложения;
2. регистрация типа приложения;
3. создание экземпляра приложения.

>[AZURE.NOTE]Если вы используете Visual Studio для развертывания и отладки приложений в локальном кластере разработки, все описанные ниже действия выполняются автоматически с помощью сценариев PowerShell, которые находятся в папке Scripts в проекте приложения. В этой статье приводятся основные сведения о действиях, выполняемых этими сценариями. Они помогут вам выполнять те же операции вне Visual Studio.

## Загрузка пакета приложения

Отправка пакета приложения означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric. Для выполнения отправки можно использовать PowerShell. Перед выполнением команд PowerShell, описанных в этой статье, нужно подключиться к кластеру Service Fabric с помощью команды **Connect-ServiceFabricCluster**.

Предположим, у вас имеется папка *MyApplicationType*, содержащая необходимый манифест приложения, манифесты служб и пакеты данных, конфигурации и кода. Команда **Copy-ServiceFabricApplicationPackage** служит для отправки пакета. Например:

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## Регистрация пакета приложения

При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, загруженного на предыдущем этапе, проверяет пакет (эквивалентно локальному выполнению команды **Test-ServiceFabricApplicationPackage**), обрабатывает содержимое пакета и копирует обработанный пакет во внутреннее системное расположение.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Команда **Register-ServiceFabricApplicationType** возвращает данные только после того, как система успешно завершит копирование пакета приложения. Срок выполнения команды зависит от содержимого пакета приложения. При необходимости можно увеличить время ожидания, используя параметр **-TimeoutSec** (значение по умолчанию — 60 секунд).

Чтобы вывести список успешно зарегистрированных версий типов приложения, используйте команду **Get-ServiceFabricApplicationType**.

## Создание приложения

Для создания экземпляра приложения можно использовать любую версию типа приложения, успешно зарегистрированную с помощью команды **New-ServiceFabricApplication**. Имя приложения должно начинаться со схемы *fabric:* и быть уникальным для каждого экземпляра приложения. Если в манифесте приложения для конкретного его типа были определены используемые по умолчанию службы, они также будут созданы.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

Команда **Get-ServiceFabricApplication** выводит список всех успешно созданных экземпляров приложения вместе с их общим состоянием.

Команда **Get-ServiceFabricService** выводит список всех экземпляров службы, созданных в рамках конкретного экземпляра приложения. В этот же список будут включены службы по умолчанию (если имеются).

Для каждой версии зарегистрированного типа приложения можно создать несколько экземпляров приложения. Каждый экземпляр будет выполняться изолированно, используя собственный рабочий каталог и процесс.

## Удаление приложения

Экземпляр приложения, который больше не требуется, можно удалить без возможности восстановления с помощью команды **Remove-ServiceFabricApplication**. При этом все службы, относящиеся к этому приложению, и все их состояния также будут удалены. Эта операция необратима, и вы не сможете восстановить состояние приложения.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Если определенная версия типа приложения больше не требуется, отмените ее регистрацию, используя команду **Unregister-ServiceFabricApplicationType**. Отмена регистрации неиспользуемых типов поможет освободить пространство, занимаемое в хранилище образов содержимым пакета приложения, относящимся к этому типу. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## Устранение неполадок

### Команда Copy-ServiceFabricApplicationPackage запрашивает строку ImageStoreConnectionString

В пакете разработки Service Fabric SDK уже предусмотрены все необходимые значения по умолчанию. Тем не менее, при необходимости значение ImageStoreConnectionString для всех команд должно совпадать со значением, используемым кластером Service Fabric. Его можно найти в манифесте кластера, используя команду **Get-ServiceFabricClusterManifest**:

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## Дальнейшие действия

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

[Общие сведения о работоспособности Service Fabric](service-fabric-health-introduction.md)

[Диагностика и устранение неполадок службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_1217_2015-->
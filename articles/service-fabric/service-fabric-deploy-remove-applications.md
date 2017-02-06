---
title: "Развертывание приложения Service Fabric | Документация Майкрософт"
description: "Развертывание и удаление приложений в Service Fabric"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 286a4f46e19b9ed38f9920e169bdadd013186ef6


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Развертывание и удаление приложений с помощью PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

После [создания пакета приложения][10] его можно развернуть в кластере Azure Service Fabric. Развертывание включает следующие три шага:

1. Загрузка пакета приложения
2. регистрация типа приложения;
3. создание экземпляра приложения.

> [!NOTE]
> Если вы используете Visual Studio для развертывания и отладки приложений в локальном кластере разработки, то все описанные ниже действия выполняются автоматически с помощью определенного сценария PowerShell из папки Scripts в проекте приложения. В этой статье приводятся основные сведения о действиях, выполняемых этими сценариями. Они помогут вам выполнять те же операции вне Visual Studio.
> 
> 

## <a name="upload-the-application-package"></a>Загрузка пакета приложения
Отправка пакета приложения означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric. Для выполнения отправки можно использовать PowerShell. Перед выполнением команд PowerShell, описанных в этой статье, нужно подключиться к кластеру Service Fabric с помощью команды [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/servicefabric/vlatest/connect-servicefabriccluster).

Предположим, у вас имеется папка *MyApplicationType* , содержащая необходимый манифест приложения, манифесты служб и пакеты данных, конфигурации и кода. Команда [Copy-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) служит для передачи пакета в хранилище образов кластера. Командлет **Get-ImageStoreConnectionStringFromClusterManifest** , являющийся частью модуля PowerShell пакета SDK для Service Fabric, позволяет получить строку подключения хранилища образов.  Чтобы импортировать модуль пакета SDK, выполните следующую команду.

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Можно скопировать пакет приложения из *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* в *c:\temp\MyApplicationType* (переименуйте каталог debug в MyApplicationType). В данном примере передается пакет.

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
    |   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
    |         Microsoft.ServiceFabric.Internal.Strings.resources.dll
    |         System.Fabric.Strings.resources.dll
    |
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, переданного на предыдущем этапе, проверяет пакет (эквивалентно локальному выполнению команды [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage)), обрабатывает содержимое пакета и копирует обработанный пакет во внутреннее системное расположение.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Команда [Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) возвращает данные только после того, как система успешно завершит копирование пакета приложения. Срок выполнения команды зависит от содержимого пакета приложения. При необходимости можно увеличить время ожидания, используя параметр **-TimeoutSec** . (значение по умолчанию — 60 секунд).

Чтобы вывести список успешно зарегистрированных версий типов приложения, используйте команду [Get-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationtype).

## <a name="create-the-application"></a>Создание приложения
Для создания экземпляра приложения можно использовать любую версию типа приложения, успешно зарегистрированную с помощью команды [New-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication). Имя приложения должно начинаться со схемы *fabric:* и быть уникальным для каждого экземпляра приложения. Если в манифесте приложения для конкретного его типа были определены используемые по умолчанию службы, они также будут созданы.

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

Команда [Get-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplication) выводит список всех успешно созданных экземпляров приложения вместе с их общим состоянием.

Команда [Get-ServiceFabricService](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservice) выводит список всех экземпляров службы, созданных в рамках конкретного экземпляра приложения. Этот список содержит службы по умолчанию (если имеются).

Для каждой версии зарегистрированного типа приложения можно создать несколько экземпляров приложения. Каждый экземпляр выполняется изолированно, используя собственный рабочий каталог и процесс.

## <a name="remove-an-application"></a>Удаление приложения
Экземпляр приложения, который больше не требуется, можно удалить без возможности восстановления с помощью команды [Remove-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication). При этом все службы, относящиеся к этому приложению, и все их состояния также будут удалены. Эта операция необратима, и вы не сможете восстановить состояние приложения.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Если определенная версия типа приложения больше не требуется, отмените ее регистрацию, используя команду [Unregister-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). Отмена регистрации неиспользуемых типов поможет освободить пространство, занимаемое в хранилище образов содержимым пакета приложения, относящимся к этому типу. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

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

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Команда Copy-ServiceFabricApplicationPackage запрашивает строку ImageStoreConnectionString
В пакете разработки Service Fabric SDK уже предусмотрены все необходимые значения по умолчанию. Тем не менее, при необходимости значение ImageStoreConnectionString для всех команд должно совпадать со значением, используемым кластером Service Fabric. Его можно найти в манифесте кластера, используя команду [Get-ServiceFabricClusterManifest](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest):

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

## <a name="next-steps"></a>Дальнейшие действия
[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

[Общие сведения о работоспособности Service Fabric](service-fabric-health-introduction.md)

[Диагностика и устранение неполадок службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md



<!--HONumber=Dec16_HO2-->



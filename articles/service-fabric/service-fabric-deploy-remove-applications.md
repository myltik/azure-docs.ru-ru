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
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Развертывание и удаление приложений с помощью PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

После [создания пакета приложения][10] его можно развернуть в кластере Azure Service Fabric. Развертывание включает следующие три шага:

1. передача пакета приложения в хранилище образов;
2. регистрация типа приложения;
3. создание экземпляра приложения.

После развертывания приложения и запуска экземпляра в кластере вы можете удалить экземпляр приложения и тип приложения. Полное удаление приложения из кластера включает следующие действия:

1. удаление запущенного экземпляра приложения;
2. отмена регистрации типа приложения, если он больше не нужен;
3. Удаление пакета приложения из хранилища образов.

Если вы используете [Visual Studio для развертывания и отладки приложений](service-fabric-publish-app-remote-cluster.md) в локальном кластере разработки, все описанные выше действия выполняются автоматически с помощью сценария PowerShell.  Этот сценарий находится в папке *Scripts* проекта приложения. Эта статья содержит основные сведения о действиях, выполняемых этим сценарием. Они помогут вам выполнять те же операции вне Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Подключение к кластеру
Перед выполнением команд PowerShell, описанных в этой статье, нужно подключиться к кластеру Service Fabric с помощью команды [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster). Чтобы подключиться к локальному кластеру разработки, выполните следующую команду:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Примеры подключения к удаленному кластеру или кластеру, защищенному с помощью Azure Active Directory, сертификатов X509 или Windows Active Directory, см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Загрузка пакета приложения
Отправка пакета приложения означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric. Если вы хотите проверить пакет приложения локально, используйте командлет [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  Команда [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) передает пакет приложения в хранилище образов кластера. Командлет **Get-ImageStoreConnectionStringFromClusterManifest** , являющийся частью модуля PowerShell пакета SDK для Service Fabric, позволяет получить строку подключения хранилища образов.  Чтобы импортировать модуль пакета SDK, выполните следующую команду.

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Предположим, вы собрали и упаковали в Visual Studio приложение с именем *MyApplication*. По умолчанию имя типа приложения отображается в файле ApplicationManifest.xml как MyApplicationType.  Пакет приложения, который содержит необходимый манифест приложения, манифесты служб и пакеты code/config/data, находится в папке *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Следующая команда выводит содержимое пакета приложения:

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Следующий пример передает пакет в папку MyApplicationV1 хранилища образов:

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

Если вы не укажете параметр *-ApplicationPackagePathInImageStore*, пакет приложения будет скопирован в папку Debug хранилища образов.

В статье [Общие сведения о параметре ImageStoreConnectionString](service-fabric-image-store-connection-string.md) вы найдете дополнительные сведения о хранилище образов и строке подключения к этому хранилищу.

## <a name="register-the-application-package"></a>Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, переданного на предыдущем этапе, проверяет пакет, обрабатывает его содержимое и копирует обработанный пакет во внутреннее системное расположение.  

Выполните команду [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), чтобы зарегистрировать тип приложения в кластере и сделать его доступным для развертывания:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

MyApplicationV1 — это папка в хранилище образов, в которой находится пакет приложения. Тип приложения с именем MyApplicationType и версией&1;.0.0 (оба параметра находятся в манифесте приложения) зарегистрирован в кластере.

Команда [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) возвращает данные только после того, как система успешно зарегистрирует пакет приложения. Продолжительность регистрации зависит от размера и содержимого пакета приложения. При необходимости можно увеличить время ожидания, указав параметр **-TimeoutSec** (по умолчанию время ожидания составляет 60 секунд).  Если размер пакета настолько большой, что приводит к превышению времени ожидания, используйте параметр **-Async**.

Чтобы вывести список зарегистрированных версий типов приложения и их состояние регистрации, используйте команду [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype).

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>Создание приложения
Чтобы создать экземпляр приложения, можно использовать любую зарегистрированную версию типа приложения. Для этого выполните командлет [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). Имя приложения должно начинаться со схемы *fabric:* и быть уникальным для каждого экземпляра приложения. Если в манифесте приложения для конкретного его типа были определены службы по умолчанию, то они также будут созданы.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Для каждой версии зарегистрированного типа приложения можно создать несколько экземпляров приложения. Каждый экземпляр выполняется изолированно, используя собственный рабочий каталог и процесс.

Чтобы увидеть, какие приложения и службы работают в кластере, выполните командлеты [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) и [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice):

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Удаление приложения
Экземпляр приложения, который больше не нужен, можно удалить без возможности восстановления с помощью командлета [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). Командлет [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) также удаляет все службы, которые относятся к этому приложению, и все их состояния. Эта операция необратима, и вы не сможете восстановить состояние приложения.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Отмена регистрации типа приложения
Если определенная версия типа приложения больше не требуется, отмените регистрацию типа приложения, используя командлет [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). Отмена регистрации неиспользуемых типов приложений помогает освободить пространство в хранилище, которое использует хранилище образов. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

Чтобы просмотреть все типы приложений, зарегистрированных в кластере, запустите командлет [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype):

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Запустите командлет [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), чтобы отменить регистрацию приложения определенного типа:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>Удаление пакета приложения из хранилища образов
Если пакет приложения больше не нужен, его можно удалить из хранилища образов, чтобы освободить системные ресурсы.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Команда Copy-ServiceFabricApplicationPackage запрашивает строку ImageStoreConnectionString
В пакете разработки Service Fabric SDK уже предусмотрены все необходимые значения по умолчанию. Тем не менее, при необходимости значение ImageStoreConnectionString для всех команд должно совпадать со значением, используемым кластером Service Fabric. Значение ImageStoreConnectionString можно найти в манифесте кластера, используя команду [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest):

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

Ниже представлено значение ImageStoreConnectionString из манифеста кластера.

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>Дальнейшие действия
[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

[Общие сведения о работоспособности Service Fabric](service-fabric-health-introduction.md)

[Диагностика и устранение неполадок службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md


---
title: "Развертывание приложения Azure Service Fabric | Документация Майкрософт"
description: "Используйте API-интерфейсы FabricClient для развертывания и удаления приложений в Service Fabric."
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
ms.date: 04/10/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e6c6b4acbc48cfbb8c26d09852f8b4c86fd6ea27
ms.lasthandoff: 04/27/2017


---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Развертывание и удаление приложений с помощью FabricClient
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [API-интерфейсы FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
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
Подключитесь к кластеру, создав экземпляр [FabricClient](/dotnet/api/system.fabric.fabricclient) перед запуском всех примеров кода в этой статье. Примеры подключения к локальному кластеру разработки, удаленному кластеру или кластеру, защищенному с помощью Azure Active Directory, сертификатов X509 или Windows Active Directory, см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis).  Чтобы подключиться к локальному кластеру разработки, выполните следующую команду:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Загрузка пакета приложения
Предположим, вы собрали и упаковали в Visual Studio приложение с именем *MyApplication*. По умолчанию имя типа приложения отображается в файле ApplicationManifest.xml как MyApplicationType.  Пакет приложения, который содержит необходимый манифест приложения, манифесты служб и пакеты code/config/data, находится в папке *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*.

Отправка пакета приложения означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric.
Если вы хотите проверить пакет приложения локально, используйте командлет [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps).

Метод [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) передает пакет приложения в хранилище образов кластера. 

Если пакет приложения большой или содержит большое количество файлов, с помощью PowerShell его можно [Сжать](service-fabric-package-apps.md#compress-a-package) и скопировать в хранилище образов. Сжатие уменьшает размер и количество файлов.

В статье [Общие сведения о параметре ImageStoreConnectionString](service-fabric-image-store-connection-string.md) вы найдете дополнительные сведения о хранилище образов и строке подключения к этому хранилищу.

## <a name="register-the-application-package"></a>Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, переданного на предыдущем этапе, проверяет пакет, обрабатывает его содержимое и копирует обработанный пакет во внутреннее системное расположение.  

Метод [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) регистрирует тип приложения кластере и делает его доступным для развертывания.

Чтобы вывести список зарегистрированных версий типов приложения и их состояние регистрации, используйте метод [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync). Эта команда используется, чтобы определить, что регистрация выполнена.

## <a name="create-an-application-instance"></a>Создание экземпляра приложения
Чтобы создать экземпляр приложения, можно использовать любую зарегистрированную версию типа приложения. Для этого выполните метод [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync). Имя приложения должно начинаться со схемы *fabric:* и быть уникальным для каждого экземпляра приложения. Если в манифесте приложения для конкретного его типа были определены службы по умолчанию, то они также будут созданы.

Для каждой версии зарегистрированного типа приложения можно создать несколько экземпляров приложения. Каждый экземпляр выполняется изолированно, используя собственный рабочий каталог и процесс.

Чтобы увидеть, что нужные приложения и службы в кластере работают, выполните методы [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) и [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync).

## <a name="create-a-service-instance"></a>Создание экземпляра службы
Экземпляр службы можно создать из типа службы с помощью метода [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync).  Если служба в манифесте приложения объявляется службой по умолчанию, экземпляр этой службы создается с помощью экземпляра приложения.  Вызов метода [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) для уже созданной службы возвращает исключение. 

## <a name="remove-a-service-instance"></a>Удаление экземпляра службы
Если экземпляр службы больше не нужен, его можно удалить из выполняемого экземпляра приложения путем вызова метода [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync).  Эта операция необратима, и вы не сможете восстановить состояние службы.

## <a name="remove-an-application-instance"></a>Удаление экземпляра приложения
Экземпляр приложения, который больше не нужен, можно удалить без возможности восстановления с помощью метода [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync). [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) автоматически удаляет все службы, относящиеся к этому приложению, и все их состояния. Эта операция необратима, и вы не сможете восстановить состояние приложения.

## <a name="unregister-an-application-type"></a>Отмена регистрации типа приложения
Если определенная версия типа приложения больше не требуется, отмените регистрацию типа приложения, используя метод [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync). Отмена регистрации неиспользуемых типов приложений помогает освободить пространство в хранилище, которое использует хранилище образов. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

## <a name="remove-an-application-package-from-the-image-store"></a>Удаление пакета приложения из хранилища образов
Если пакет приложения больше не нужен, его можно удалить из хранилища образов, чтобы освободить системные ресурсы с помощью метода [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage).

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Команда Copy-ServiceFabricApplicationPackage запрашивает строку ImageStoreConnectionString
В пакете разработки Service Fabric SDK уже предусмотрены все необходимые значения по умолчанию. Тем не менее, при необходимости значение ImageStoreConnectionString для всех команд должно совпадать со значением, используемым кластером Service Fabric. Значение ImageStoreConnectionString можно найти в манифесте кластера, используя метод [Get-ServiceFabricClusterManifest](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.getclustermanifestasync).

Ниже представлено значение ImageStoreConnectionString из манифеста кластера.

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

В статье [Общие сведения о параметре ImageStoreConnectionString](service-fabric-image-store-connection-string.md) вы найдете дополнительные сведения о хранилище образов и строке подключения к этому хранилищу.

### <a name="deploy-large-application-package"></a>Развертывание пакета приложения большего размера
Проблема. Время ожидания метода [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) истекает для пакета большого приложения (несколько ГБ).
Попробуйте выполнить следующее.
- Задайте больше времени ожидания для метода [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) с помощью параметра `timeout`. По умолчанию время ожидания составляет 30 минут.
- Проверьте сетевое подключение между исходным компьютером и кластером. Если подключение медленное, рассмотрите возможность использовать машину с лучшим сетевым соединением.
Возможно, клиентский компьютер находится не в одном регионе с кластером, тогда перейдите на компьютер, который находится с ним в одном регионе или в регионе поблизости.
- Проверьте, достигнуто ли внешнее регулирование. Например, если хранилище образов настроено для использования хранилища Аzure, загрузку можно регулировать.

Проблема. Отправка пакета завершена успешно, но время ожидания метода [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) истекло.
Попробуйте выполнить следующее.
- [Выполните сжатие пакета](service-fabric-package-apps.md#compress-a-package) перед копированием в хранилище образов.
Сжатие уменьшает размер и число файлов, что, в свою очередь, снижает объем трафика и работу, которую необходимо выполнить Service Fabric. Операция загрузки может выполняться медленнее (особенно при выполнении сжатия), но регистрация и отмена регистрации типа приложения выполняются быстрее.
- Задайте больше времени ожидания для метода [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) с помощью параметра `timeout`.

### <a name="deploy-application-package-with-many-files"></a>Развертывание пакета приложения с несколькими файлами
Проблема. Время ожидания метода [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) для пакета приложения со множеством (несколько тысяч) файлов истекло.
Попробуйте выполнить следующее.
- [Выполните сжатие пакета](service-fabric-package-apps.md#compress-a-package) перед копированием в хранилище образов. Сжатие уменьшает количество файлов.
- Задайте больше времени ожидания для метода [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) с помощью параметра `timeout`.

## <a name="code-example"></a>Примеры кода
В следующем примере пакет приложения копируется в хранилище образов, подготавливается тип приложения, создается экземпляр приложения, создается экземпляр службы, удаляется экземпляр приложения, отменяется подготовка типа приложения, а также из хранилища образов удаляется пакет приложения.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the app package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Дальнейшие действия
[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

[Общие сведения о работоспособности Service Fabric](service-fabric-health-introduction.md)

[Диагностика и устранение неполадок службы Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md


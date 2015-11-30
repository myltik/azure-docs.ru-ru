<properties
   pageTitle="Reliable Actors в Service Fabric | Microsoft Azure"
   description="Описывает, как субъекты Reliable Actors используют компоненты платформы Service Fabric, охватывая основные понятия с точки зрения разработчиков субъектов."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="abhisram"/>

# Использование платформы Service Fabric надежными субъектами

Субъекты используют модель приложений Service Fabric для управления жизненным циклом приложения. Каждый тип субъекта сопоставляется с [типом службы](service-fabric-application-model.md#describe-a-service) Service Fabric. Код субъекта [упаковывается](service-fabric-application-model.md#package-an-application) в виде приложения Service Fabric и [развертывается](service-fabric-deploy-remove-applications.md#deploy-an-application) в кластере.

## Пример концепции модели приложений для субъектов

Рассмотрим пример проекта субъекта, [созданный с помощью Visual Studio](service-fabric-reliable-actors-get-started.md), чтобы проиллюстрировать некоторые описанные выше понятия.

Манифест приложения, манифест службы и файл конфигурации Settings.xml включаются в проект службы субъекта при создании решения в Visual Studio. Это показано на приведенном ниже снимке экрана.

![][1]

Тип приложения и версию приложения, в которое упакован субъект, можно найти, просмотрев манифест приложения, включенный в проект для службы субъекта. Примером этого является следующий фрагмент манифеста приложения.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

Тип службы, с которым сопоставляется тип субъекта, можно найти, просмотрев манифест службы, включенный в проект для службы субъекта. Примером этого является следующий фрагмент манифеста службы.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Когда создается пакет приложения с помощью Visual Studio, в журналах в окне выходных данных построения можно найти информацию о расположении пакета приложения. Например:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Ниже приведен частичный листинг указанного выше расположения (полный листинг опущен для краткости).

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───ru-RU
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───ru-RU
                    System.Fabric.Common.Internal.Strings.resources.dll

В листинге выше отображаются сборки, которые реализуют субъект VoicemailBox, включаемый в пакет кода внутри пакета службы, который находится внутри пакета приложения.

Последующее управление приложением (т. е. обновления и окончательное удаление) выполняется также с помощью механизмов управления приложениями Service Fabric. Дополнительные сведения см. в разделах о [модели приложений](service-fabric-application-model.md), [развертывании и удалении приложений](service-fabric-deploy-remove-applications.md) и [обновлении приложений](service-fabric-application-upgrade.md).

## Масштабируемость для служб субъектов
Администраторы кластера могут создать одну или несколько служб субъектов для каждого типа службы в кластере. Каждая из этих служб субъектов может иметь одну или несколько секций (аналогично любой другой службе Service Fabric). Возможность создания нескольких служб одного типа (который сопоставляется с типом субъекта) и возможность создания нескольких секций для службы позволяют масштабировать приложение субъекта. Для получения дополнительных сведений см. статью о [масштабируемости](service-fabric-concepts-scalability.md).

> [AZURE.NOTE]Количество [экземпляров](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) служб субъекта без отслеживания состояния должно быть равным 1. Наличие более одного экземпляра службы субъекта без отслеживания состояний в секции не поддерживается. Таким образом службы субъекта без отслеживания состояний не имеют возможности увеличения количества экземпляров для достижения масштабируемости. Они должны использовать параметры масштабирования, описанные в [статье о масштабируемости](service-fabric-concepts-scalability.md).

## Основные понятия о секции Service Fabric для субъектов
Идентификатор субъекта сопоставляется с секцией службы субъекта. Субъект создается в пределах секции, с которой сопоставлен его идентификатор. При создании субъекта среда выполнения субъектов записывает [событие EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) указывающее, в какой секции создается субъект. Ниже приведен пример этого события, который указывает, что субъект с идентификатором `-5349766044453424161` создан в разделе `b6afef61-be9a-4492-8358-8f473e5d2487` службы `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService` (приложение `fabric:/VoicemailBoxAdvancedApplication`).

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Другой субъект с идентификатором `-4952641569324299627` создан в другой секции `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a` той же службы, о чем свидетельствует событие ниже.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*Примечание.* Для краткости некоторые поля указанных выше событий опущены.

Идентификатор секции может использоваться для получения других сведений о секции. Например, с помощью средства [Обозреватель Service Fabric](service-fabric-visualizing-your-cluster.md) можно просмотреть сведения о секции, а также о службе и приложении, к которым она принадлежит. На следующем снимке экрана показаны сведения о секции `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`, которая содержала субъект с идентификатором `-4952641569324299627` в приведенном выше примере.

![][3]

Субъекты могут программным образом получить идентификатор секции, имя службы, имя приложения и другие сведения для конкретной платформы Service Fabric через `Host.ActivationContext` и членов `Host.StatelessServiceInitialization` или `Host.StatefulServiceInitializationParameters` базового класса, от которого тип субъекта является производным. В приведенном ниже фрагменте кода показан пример.

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### Основные понятия о секциях Service Fabric для субъектов без отслеживания состояний
Субъекты без отслеживания состояний создаются внутри раздела службы Service Fabric без отслеживания состояний. Идентификатор субъекта определяет, под каким разделом создается субъект. Количество [экземпляров](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) служб субъекта без отслеживания состояния должно быть равным 1. Изменение количества экземпляров на любое другое значение не поддерживается. Таким образом, субъект создается внутри одного экземпляра службы в секции.

> [AZURE.TIP]Среда выполнения субъектов Fabric генерирует некоторые [события, связанные с экземплярами субъектов без отслеживания состояния](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances). Они полезны при диагностике и мониторинге производительности.

При создании субъекта без отслеживания состояния среда выполнения субъектов записывает [событие EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events), указывающее, в какой секции и в каком экземпляре создается субъект. Ниже приведен пример этого события, который указывает, что субъект с идентификатором `abc` создан в экземпляре `130745709600495974` секции `8c828833-ccf1-4e21-b99d-03b14d4face3` службы `fabric:/HelloWorldApplication/HelloWorldActorService` (приложение `fabric:/HelloWorldApplication`).

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*Примечание.* Для краткости некоторые поля указанного выше события опущены.

### Основные понятия о секциях Service Fabric для субъектов с отслеживанием состояний
Субъекты с отслеживанием состояний создаются внутри раздела службы Service Fabric с отслеживанием состояний. Идентификатор субъекта определяет, под каким разделом создается субъект. Каждая секция службы может иметь одну или несколько [реплик](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services), которые размещаются на разных узлах кластера. Наличие нескольких реплик повышает надежность состояния субъекта. Диспетчер ресурсов оптимизирует размещение в зависимости от имеющегося сбоя и доменов обновления в кластере. Две реплики одной секции никогда не размещаются на одном узле. Субъекты всегда создаются в основной реплике секции, с которой сопоставляется их идентификатор.

> [AZURE.TIP]Среда выполнения субъектов Fabric генерирует некоторые [события, связанные с репликами субъекта с отслеживанием состояния](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas). Они полезны при диагностике и мониторинге производительности.

Помните, что в [ранее представленном примере VoiceMailBoxActor](#service-fabric-partition-concepts-for-actors) субъект с идентификатором `-4952641569324299627` был создан в секции `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`. Событие EventSource из этого примера указало также то, что субъект создан в реплике `130745418574851853` этой секции. Она была основной репликой этой секции во время создания субъекта. На следующем снимке экрана Service Fabric Explorer это подтверждается.

![][4]

## Выбор поставщиков состояний субъекта
Существует несколько поставщиков состояний субъектов, которые используются по умолчанию и являются частью среды выполнения субъектов. Чтобы выбрать соответствующего поставщика состояний для службы субъекта, необходимо понимать, как поставщики состояний используют базовые возможности платформы Service Fabric для обеспечения высокой доступности состояния субъекта.

По умолчанию субъект с отслеживанием состояния использует в качестве поставщика состояний субъекта хранилище ключей и значений. Этот поставщик построен на основе распределенного хранилища ключей и значений, предоставляемого платформой Service Fabric. Состояние надежно сохраняется на локальном диске узла, где размещена основная [реплика](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services), а также реплицируется и надежно сохраняется на локальных дисках узлов, где размещены вторичные реплики. Сохранение состояния завершается только в том случае, если кворум реплик зафиксировал состояние на своих локальных дисках. Хранилище ключей и значений обладает расширенными возможностями для обнаружения и автоматического исправления несоответствий, таких как ложный ход выполнения.

В среду выполнения субъектов входит также `VolatileActorStateProvider`. Этот поставщик состояний реплицирует состояние по репликам, но состояние остается в памяти реплики. Если одна реплика отключится, а затем возобновит работу, ее состояние будет перестроено на основе другой реплики. Однако если все реплики (копии состояния) выйдут из строя одновременно, данные о состоянии будут потеряны. Таким образом, этот поставщик состояний подходит для приложений, где данные могут выдержать сбой нескольких реплик, а также плановую отработку их отказа, например при обновлениях. Если все реплики (копии) будут потеряны, данные необходимо будет создать заново с помощью механизмов, которые по отношению к Service Fabric являются внешними. Субъект с отслеживанием состояния можно настроить на использование неустойчивого поставщика состояний субъекта, добавив атрибут `VolatileActorStateProvider` в класс субъекта или атрибут уровня сборки.

В следующем фрагменте кода показано, как изменить все субъекты в сборке, у которой нет явного атрибута поставщика состояний для использования `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

В следующем фрагменте кода показано, как изменить поставщика состояний для определенного типа субъекта, в данном случае `VoicemailBox`, на `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Обратите внимание, что для изменения поставщика состояний требуется заново создать службу субъекта. Поставщиков состояний нельзя изменять в рамках обновления приложения.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=Nov15_HO4-->
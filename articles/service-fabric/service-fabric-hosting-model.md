---
title: "Модель размещения Azure Service Fabric | Документация Майкрософт"
description: "В этой статье описывается связь между репликами (или экземплярами) развернутой службы Service Fabric и процессом размещения службы."
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ca7092a06a9ffce8383ca8bc9f70ce312cdf9de4
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="service-fabric-hosting-model"></a>Модель размещения Service Fabric
В этой статье представлен обзор моделей размещения приложений, предоставленных Service Fabric, и приведены различия между моделями **с общим** и **монопольным процессом**. Здесь также описывается, как развернутое приложение выглядит на узле Service Fabric, и рассматриваются связи между репликами (или экземплярами) службы и процессом размещения служб.

Прежде чем продолжить, убедитесь, что вы знакомы с [моделированием приложения Service Fabric][a1] и понимаете различные понятия и отношения между ними. 

> [!NOTE]
> Если явно не указано иное, для простоты в этой статье:
>
> - все случаи использования слова *реплика* относятся к реплике службы с отслеживанием состояния или экземпляру службы без отслеживания состояния;
> - *CodePackage* считается эквивалентом процесса *ServiceHost*, который регистрирует *ServiceType* и размещает реплики служб этого *ServiceType*.
>

Чтобы понять модель размещения, давайте подробно рассмотрим пример. Допустим, у нас есть *тип приложения* MyAppType, который имеет *тип службы* MyServiceType, предоставляемый *пакетом службы* MyServicePackage, который имеет *пакет кода* MyCodePackage, регистрирующий при запуске *тип службы* MyServiceType.

Допустим, у нас есть кластер из 3 узлов и мы создаем *приложение* **fabric:/App1** типа MyAppType. Внутри этого *приложения* **fabric:/App1** мы создаем службу **fabric:/App1/ServiceA** типа MyServiceType, которая имеет 2 раздела (например, **P1** & **P2**) с 3 репликами на каждый. На следующей схеме показано представление этого приложения, развернутого на узле.

<center>
![Представление узла развернутого приложения][node-view-one]
</center>

Service Fabric активирует MyServicePackage, который запустил MyCodePackage, где размещаются реплики из обоих разделов, т. е. **P1** & **P2**. Обратите внимание, что все узлы в кластере будут иметь одинаковое представление, так как мы выбрали число реплик на раздел равное числу узлов в кластере. Давайте создадим другую службу **fabric:/App1/ServiceB** в приложении **fabric:/App1**, которая имеет 1 раздел (например, **P3**) с 3 репликами. На следующей схеме показано новое представление узла.

<center>
![Представление узла развернутого приложения][node-view-two]
</center>

Как мы видим, Service Fabric поместил новую реплику раздела **P3** службы **fabric:/App1/ServiceB** в существующую активацию MyServicePackage. Теперь создадим другое *приложение* **fabric:/App2** типа MyAppType, а внутри **fabric:/App2** создадим службу **fabric:/App2/ServiceA**, которая имеет 2 раздела (например, **P4** & **P5**) с 3 репликами на каждый. На следующих схемах показано новое представление узла:

<center>
![Представление узла развернутого приложения][node-view-three]
</center>

На этот раз Service Fabric активировал новую копию MyServicePackage, которая запускает новую копию MyCodePackage, а реплики из обоих разделов службы **fabric:/App2/ServiceA** (т. е. **P4** & **P5**) размещаются в этой новой копии MyCodePackage.

## <a name="shared-process-model"></a>Модель с общим процессом
Выше была описана модель размещения по умолчанию, предоставляемая службой Service Fabric, которая называется моделью **с общим процессом**. В этой модели для заданного *приложения* активируется только одна копия заданного *ServicePackage* на *узле* (которая запускает все содержащиеся в ней пакеты *CodePackage*), а все реплики всех служб заданного *ServiceType* размещаются в *CodePackage*, который регистрирует *ServiceType*. Другими словами, все реплики всех служб заданного *ServiceType* совместно используют один процесс.

## <a name="exclusive-process-model"></a>Модель с монопольным процессом
Другой моделью размещения, представляемой Service Fabric, является модель с **монопольным процессом**. В этой модели на заданном *узле* для размещения каждой реплики Service Fabric активирует новую копию *ServicePackage* (которая запускает все содержащиеся в ней пакеты *CodePackage*), а реплика размещается в *CodePackage*, который регистрирует *ServiceType* службы, к которой принадлежит реплика. Другими словами, каждая реплика находится в отдельном выделенном процессе. 

Эта модель поддерживается в Service Fabric, начиная с версии 5.6. Модель с **монопольным процессом** можно выбрать во время создания службы (с помощью [PowerShell][p1], [REST][r1] или [FabricClient][c1]), указав для параметра **ServicePackageActivationMode** значение ExclusiveProcess.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Если в манифесте приложения указана служба по умолчанию, можно выбрать модель с **монопольным процессом**, указав атрибут **ServicePackageActivationMode**, как показано ниже.

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Далее создадим другую службу **fabric:/App1/ServiceC** в приложении **fabric:/App1**, которая имеет 2 раздела (например, **P6** & **P7**) с 3 репликами на каждый со значением параметра **ServicePackageActivationMode** равным ExclusiveProcess. На следующей схеме показано новое представление узла.

<center>
![Представление узла развернутого приложения][node-view-four]
</center>

Как видите, Service Fabric активировал две новые копии MyServicePackage (по одной на каждую реплику из раздела **P6** & **P7**) и разместил каждую реплику в выделенную копию *CodePackage*. Также следует помнить, что при использовании модели с **монопольным процессом** для заданного *приложения* на *узле* могут быть активны несколько копий заданного *ServicePackage*. В предыдущем примере мы видим, что для **fabric:/App1** активны три копии MyServicePackage. Каждая из этих активных копий MyServicePackage имеет связанный с ней **ServicePackageAtivationId**, который определяет эту копию в *приложении* **fabric:/App1**.

Если для *приложения* (например, **fabric:/App2** в предыдущем примере) используется только модель с **общим процессом**, на *узле* будет только один активный экземпляр *ServicePackage*, а значение **ServicePackageAtivationId** для этой активации *ServicePackage* будет соответствовать пустой строке.

> [!NOTE]
>- Модель размещения с **общим процессом** используется, когда для параметра **ServicePackageActivationMode** задано значение **SharedProcess**. Это модель размещения по умолчанию, поэтому во время создания службы необходимо указать значение **ServicePackageActivationMode**.
>
>- Модель размещения с **монопольным процессом** используется, когда для параметра **ServicePackageActivationMode** явно задано значение **ExclusiveProcess** во время создания службы. 
>
>- Модель размещения службы можно узнать, запросив [описание службы][p2], где нужно просмотреть значение **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Работа с развернутым пакетом службы
Активная копия *ServicePackage* на узле называется [развернутым пакетом службы][p3]. Как упоминалось выше, когда для создания служб используется модель с **монопольным процессом**, для заданного *приложения* можно развернуть несколько пакетов служб одного *ServicePackage*. Во время выполнения операций, относящихся к развернутому пакету службы (например, [отправки сведений о работоспособности развернутого пакета службы][p4] или при [перезапуске пакета кода развернутого пакета службы][p5]), необходимо предоставить значение **ServicePackageActivationId** для идентификации конкретного развернутого пакета службы.

 **ServicePackageActivationId** развернутого пакета службы можно получить, запросив список [развернутых пакетов служб][p3] на узле. При запросе [типов развернутых служб][p6], [развернутых реплик][p7] и [развернутых пакетов кода][p8] на узле результат запроса также содержит значение **ServicePackageActivationId** родительского развернутого пакета службы.

> [!NOTE]
>- В модели размещения с **общим процессом** на заданном *узле* заданного *приложения* активируется только одна копия *ServicePackage*. При этом параметр **ServicePackageActivationId** соответствует *пустой строке*, и его необходимо указать при выполнении операций, связанных с развернутым пакетом службы. 
>
> - В модели размещения с **монопольным процессом** на заданном *узле* заданного *приложения* активируется одна или несколько копий *ServicePackage*. Каждая активация имеет *непустое* значение **ServicePackageActivationId**, которое необходимо указать при выполнении операций, связанных с развернутым пакетом службы. 
>
> - Если значение **ServicePackageActivationId** опущено, по умолчанию используется пустая строка. При наличии развернутого пакета службы, который был активирован с помощью модели с **общим процессом**, операция будет выполняться с ним. В противном случае операция завершится ошибкой.
>
> - Не рекомендуется выполнять один запрос и кэшировать значение **ServicePackageActivationId**, так как оно создается динамически и может изменяться по различным причинам. Перед выполнением операции, для которой требуется значение **ServicePackageActivationId**, сначала следует запросить список [развернутых пакетов служб][p3] на узле, а затем использовать значение *ServicePackageActivationId** из результата запроса для выполнения исходной операции.
>
>

## <a name="guest-executable-and-container-applications"></a>Гостевые исполняемые приложения и приложения-контейнеры
Service Fabric рассматривает [гостевые исполняемые приложения][a2] и [приложения-контейнеры][a3] в качестве служб без отслеживания состояния, которые являются самодостаточными, т. е. для них в *ServiceHost* (в процессе или контейнере) нет среды выполнения Service Fabric. Так как эти службы являются самодостаточными, для этих служб число реплик на *ServiceHost* неприменимо. Самая распространенная конфигурация, которая используется с этими службами, — один раздел со значением [InstanceCount][c2] равным -1 (т. е. с одной копией кода службы на каждом узле кластера). 

Значением **ServicePackageActivationMode** по умолчанию для этих служб является **SharedProcess**. В этом случае для заданного *приложения* Service Fabric активирует только одну копию *ServicePackage* на *узле*. Это означает, что на *узле* будет выполняться только одна копия кода службы. Если на *узле* требуется запустить несколько копий кода службы при создании нескольких служб (от *Service1* до *ServiceN*) *ServiceType* (указанного в *ServiceManifest*) или если служба имеет несколько разделов, во время создания службы для параметра **ServicePackageActivationMode** необходимо задать значение **ExclusiveProcess**.

## <a name="changing-hosting-model-of-an-existing-service"></a>Изменение модели размещения существующей службы
Изменение модели размещения существующей службы с модели с **общим процессом** на модель с **монопольным процессом** и наоборот путем установки новой версии или обновления (или в спецификации службы по умолчанию в манифесте приложения) в настоящее время не поддерживается. Поддержка этой функции будет реализована в будущих версиях.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Выбор между моделями с общим и монопольным процессом
Обе эти модели размещения имеют свои преимущества и недостатки, которые необходимо взвесить, чтобы понять, какая из них лучше всего соответствует требованиям. Модель с **общим процессом** обеспечивает более эффективное использование ресурсов операционной системы, так как создается меньшее число процессов, несколько реплик в одном процессе могут совместно использовать порты и т. д. Тем не менее, если в одной из реплик возникает ошибка, при которой ей придется отключить узел службы, это затронет все реплики в одном процессе.

 Модель с **монопольным процессом** обеспечивает лучшую изоляцию каждой реплики в своем собственном процессе. Таким образом, некорректно работающая реплика не влияет на другие реплики. Это подходит для тех случаев, когда протокол связи не поддерживает совместное использование порта. Эта модель обеспечивает возможность управления ресурсами на уровне реплики. С другой стороны, **монопольный процесс** потребляет больше ресурсов ОС, так как для каждой реплики на узле создается один процесс.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Рекомендации по модели с монопольным процессом и модели приложения
Для моделирования приложений в Service Fabric рекомендуется иметь один *ServiceType* для каждого *ServicePackage*, так как эта модель хорошо подходит для большинства приложений. 

Тем не менее в особых случаях, когда использование одного *ServiceType* для каждого *ServicePackage* может быть нежелательно, Service Fabric позволяет иметь несколько *ServiceType* для каждого *ServicePackage* (также один *CodePackage* может зарегистрировать несколько *ServiceType*). Ниже приведены некоторые сценарии, в которых можно использовать эти конфигурации:

- Необходимо оптимизировать использование ресурсов ОС за счет запуска меньшего числа процессов с более высокой плотностью реплик для каждого из них.
- Реплики различных ServiceType должны совместно использовать некоторые общие данные с высоким потреблением памяти или затратами на инициализацию.
- У вас есть предложение с бесплатной службой, и вы хотите установить ограничение на использование ресурсов путем размещения всех реплик службы в один процесс.

Модель размещения с **монопольным процессом** не согласуется с моделью приложения, которая имеет несколько типов *ServiceType* для каждого *ServicePackage*. Это обусловлено тем, что использование нескольких типов *ServiceType* для каждого *ServicePackage* предназначено, чтобы повысить уровень совместного использования ресурсов между репликами и обеспечить более высокую плотность реплик в каждом процессе. Это кардинально отличается от той задачи, для которой предназначена модель с **монопольным процессом**.

Рассмотрим случай использования нескольких типов *ServiceType* для каждого *ServicePackage*, при котором каждый *ServiceType* регистрируется с помощью разных *CodePackage*. Предположим, у нас есть *ServicePackage* MultiTypeServicePackage, который содержит два пакета *CodePackage*:

- MyCodePackageA, который регистрирует *тип службы* MyServiceTypeA.
- MyCodePackageB, который регистрирует *тип службы* MyServiceTypeB.

Теперь создадим *приложение* **fabric:/SpecialApp** и внутри **fabric:/SpecialApp** создадим две следующие службы с помощью модели с **монопольным процессом**:

- Служба **fabric:/SpecialApp/ServiceA** типа MyServiceTypeA с 2 разделами (например, **P1** и **P2**) и 3 репликами на каждый раздел.
- Служба **fabric:/SpecialApp/ServiceB** типа MyServiceTypeB с 2 разделами (например, **P3** и **P4**) и 3 репликами на каждый раздел.

На заданном узле обе службы будут иметь две реплики. Так как для создания служб мы использовали модель с **монопольным процессом**, для каждой реплики Service Fabric активирует новую копию MyServicePackge. Каждая активация MultiTypeServicePackge запускает копию MyCodePackageA и MyCodePackageB. Однако только в одной из копий (MyCodePackageA или MyCodePackageB) будет размещена реплика, для которой был активирован MultiTypeServicePackage. На следующей схеме показано представление узла:

<center>
![Представление узла развернутого приложения][node-view-five]
</center>

 Как вы видите, в активации MultiTypeServicePackage для реплики раздела **P1** службы **fabric:/SpecialApp/ServiceA** в MyCodePackageA размещается реплика, а MyCodePackageB просто запущен и работает. Аналогичным образом в активации MultiTypeServicePackage для реплики раздела **P3** службы **fabric:/SpecialApp/ServiceB** в MyCodePackageA размещается реплика, а MyCodePackageA просто запущен и работает и т. д. Таким образом, чем больше количество пакетов *CodePackage* (регистрирующих различные типы *ServiceType*) для каждого *ServicePackage*, тем выше будет избыточное использование ресурсов. 
 
 С другой стороны, если мы создадим службы **fabric:/SpecialApp/ServiceA** и **fabric:/SpecialApp/ServiceB** с помощью модели с **общим процессом**, Service Fabric активирует только одну копию MultiTypeServicePackage для *приложения* **fabric:/SpecialApp** (как рассматривалось ранее). В MyCodePackageA разместятся все реплики службы **fabric:/SpecialApp/ServiceA** (точнее любой службы типа MyServiceTypeA), а в MyCodePackageB разместятся все реплики службы **fabric:/SpecialApp/ServiceB** (точнее любой службы типа MyServiceTypeB). На следующей схеме показано представление узла в такой конфигурации: 

<center>
![Представление узла развернутого приложения][node-view-six]
</center>

Из приведенного выше примера можно сделать вывод, что если MyCodePackageA регистрирует оба типа MyServiceTypeA и MyServiceTypeB, но пакет MyCodePackageB отсутствует, избыточный *CodePackage* выполняться не будет. Это верно, но, как упоминалось ранее, эта модель приложения не согласуется с моделью размещения с **монопольным процессом**. Если задача в том, чтобы разместить каждую реплику в свой собственный выделенный процесс, тогда регистрация обоих типов *ServiceType* из одного *CodePackage* не требуется. Будет проще поместить каждый *ServiceType* в свой собственный *ServicePackage*.

## <a name="next-steps"></a>Дальнейшие действия
[Создайте пакет приложения][a4] и подготовьте его к развертыванию.

В статье [Развертывание и удаление приложений с помощью PowerShell][a5] вы узнаете, как управлять экземплярами приложений с помощью PowerShell.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage

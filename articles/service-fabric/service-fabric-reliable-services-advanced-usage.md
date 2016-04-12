<properties
   pageTitle="Дополнительные возможности использования модели программирования надежных служб | Microsoft Azure"
   description="Модель программирования надежных служб Service Fabric: сведения о расширенных возможностях использования, с помощью которых можно повысить гибкость работы служб."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Дополнительные возможности использования модели программирования надежных служб
Приложение Azure Service Fabric упрощает написание надежных служб с отслеживанием или без отслеживания состояния и управление ими. Это руководство расскажет о дополнительных вариантах использования модели программирования надежных служб для получения большей управляемости и гибкости служб. Перед чтением этого руководства ознакомьтесь с [моделью программирования надежных служб](service-fabric-reliable-services-introduction.md).

## Базовые классы для служб без отслеживания состояния
Базовый класс StatelessService позволяет использовать методы RunAsync() и CreateServiceInstanceListeners(), которых вполне достаточно для большинства служб без отслеживания состояния. Класс StatelessServiceBase лежит в основе StatelessService и предоставляет дополнительные события жизненного цикла службы. Вы можете использовать класс StatelessServiceBase, если вам требуется дополнительный контроль или гибкость. Дополнительную информацию о [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) и [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) см. в справочной документации разработчика.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize — первый метод, вызываемый Service Fabric. Предоставляются сведения об инициализации службы: имя службы, идентификатор раздела, идентификатор экземпляра и сведения о пакете кода. Здесь не следует выполнять никакой сложной обработки. Длительную инициализацию следует выполнять в OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` Объект OnOpenAsync вызывается, когда требуется использовать экземпляр службы без отслеживания состояния. В этот момент можно запустить расширенные задачи инициализации службы.

- `Task OnCloseAsync(CancellationToken)` Объект OnCloseAsync вызывается, когда работу экземпляра службы без отслеживания состояния планируется корректно завершить. Это может произойти при обновлении кода службы, перемещении экземпляра службы в целях балансировки нагрузки или при обнаружении временной ошибки. Объект OnCloseAsync позволяет безопасно закрыть все ресурсы, остановить все фоновые задачи, завершить сохранение внешнего состояния или закрыть существующие соединения.

- `void OnAbort()` Объект OnAbort вызывается при принудительном завершении работы экземпляра службы без отслеживания состояния. Обычно такой вызов осуществляется при обнаружении на узле постоянной неисправности или когда платформа Service Fabric не может надежно управлять жизненным циклом экземпляра службы из-за внутренних сбоев.

## Базовые классы для служб с отслеживанием состояния
Базового класса StatefulService должно быть достаточно для большинства служб с отслеживанием состояния. Подобно службам без отслеживания состояния класс StatefulServiceBase лежит в основе StatefulService и предоставляет дополнительные события жизненного цикла службы. Кроме того, он позволяет предоставить настраиваемого надежного поставщика состояний и при необходимости поддерживать средства прослушивания связи на стороне дополнительных служб. Вы можете использовать класс StatefulServiceBase, если вам требуется дополнительный контроль или гибкость. Дополнительную информацию о [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) и [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) см. в справочной документации разработчика.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` Объект OnChangeRoleAsync вызывается, когда служба с отслеживанием состояния меняет роли, например с основной на дополнительную. Основным репликам присваивается статус записи (им разрешено создание и запись надежных коллекций), а дополнительным — статус чтения (могут только читать из существующих надежных коллекций). В ответ на изменения ролей можно запустить или обновить фоновые задачи — выполнение проверки, предусматривающей только чтение, создание отчетов или интеллектуальный анализ данных на стороне дополнительной службы.

- `IStateProviderReplica CreateStateProviderReplica()` Для службы с отслеживанием состояния ожидается наличие надежного поставщика состояний. Класс StatefulService пользуется классом ReliableStateManager, который дает возможность задействовать надежные коллекции (например, словари и очереди). Вы можете перезаписать этот метод для настройки класса ReliableStateManager, передав в его конструктор параметр ReliableStateManagerConfiguration. После этого можно предоставить настраиваемые сериализаторы состояний, указать, что произойдет в случае потери данных, и настроить поставщики репликаторов или состояний.

Класс StatefulServiceBase дает возможность использовать те же четыре события жизненного цикла, что и StatelessServiceBase, с такой же семантикой и вариантами использования:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Дальнейшие действия
Дополнительные темы, связанные с Service Fabric, см. в приведенных ниже статьях:

- [Настройка надежных служб с отслеживанием состояния](service-fabric-reliable-services-configuration.md)

- [Общие сведения о работоспособности Service Fabric](service-fabric-health-introduction.md)

- [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Настройка служб с помощью диспетчера кластерных ресурсов Service Fabric](service-fabric-cluster-resource-manager-configure-services.md)

<!---HONumber=AcomDC_0309_2016-->
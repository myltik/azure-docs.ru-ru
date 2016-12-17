---
title: "Таймеры и напоминания Reliable Actors | Документация Майкрософт"
description: "Общие сведения о таймерах и напоминаниях для надежных субъектов Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 56b2e13c3bf053175e357a627d45a91d9a9a4ba9


---
# <a name="actor-timers-and-reminders"></a>Таймеры и напоминания субъекта
Субъекты могут планировать для себя периодические операции, регистрируя таймеры или напоминания. В этой статье показано, как использовать таймеры и напоминания, а также объясняются различия между ними.

## <a name="actor-timers"></a>Таймеры субъектов
Таймеры субъекта обеспечивают простую оболочку для таймера .NET, чтобы методы обратного вызова учитывали гарантии пошагового параллелизма, предоставляемые средой выполнения субъектов.

Субъекты могут использовать методы `RegisterTimer` и `UnregisterTimer` в своем базовом классе для регистрации и отмены регистрации своих таймеров. В приведенном ниже примере показано использование интерфейсов API таймера. Эти интерфейсы API очень похожи на таймер .NET. В этом примере при срабатывании таймера среда выполнения субъектов вызовет метод `MoveObject` . Этот метод гарантированно учитывает пошаговый параллелизм. Это означает, что никакие другие методы субъектов или обратные вызовы таймеров или напоминаний не будут выполняться до завершения этого обратного вызова.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

Следующий период таймера начинается после завершения выполнения обратного вызова. Это подразумевает, что таймер останавливается во время выполнения обратного вызова и запускается по завершении обратного вызова.

Среда выполнения субъектов сохраняет изменения, внесенные в диспетчере состояния субъекта, по завершении обратного вызова. В случае ошибки при сохранении состояния объект данного субъекта отключается и активным становится новый экземпляр. 

При отключении субъекта в процессе сборки мусора все таймеры останавливаются. После этого обратные вызовы таймеров не выполняются. Кроме того, среда выполнения Actors не сохраняет никаких сведений о таймерах, запущенных до отключения. Регистрация таймеров, которые понадобятся субъекту при повторной активации в будущем, возлагается на субъект. Дополнительные сведения см. в статье [Сборка мусора и субъекты](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Напоминания для субъекта
Напоминания — это механизм для срабатывания постоянных обратных вызовов по субъекту в заданные моменты времени. Их функциональные возможности аналогичны таймерам. В отличие от таймеров, напоминания активируются при любых обстоятельствах, пока субъект явно не отменит их регистрацию или не удалит их. В частности, напоминания срабатывают независимо от отключения субъектов и отработки отказов, так как в среде выполнения Actors сохраняются сведения о напоминаниях субъекта.

Чтобы зарегистрировать напоминание, субъект вызывает метод `RegisterReminderAsync` , предоставленный в базовом классе, как показано в примере ниже.

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

В этом примере `"Pay cell phone bill"` — имя напоминания. Это строка, которую субъект использует для уникальной идентификации напоминания. `BitConverter.GetBytes(amountInDollars)` — контекст, связанный с напоминанием. Он будет передан обратно субъекту в качестве аргумента обратного вызова напоминания, т. е. `IRemindable.ReceiveReminderAsync`.

Субъекты, использующие напоминания, должны реализовать интерфейс `IRemindable` (см. пример ниже).

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

При активации напоминания среда выполнения Reliable Actors вызовет метод субъекта `ReceiveReminderAsync`. Субъект может зарегистрировать несколько напоминаний, и метод `ReceiveReminderAsync` будет вызываться при активации любого из них. Субъект с помощью имени напоминания, переданного методу `ReceiveReminderAsync` , может выяснить, какое напоминание сработало.

Когда вызов `ReceiveReminderAsync` будет завершен, среда выполнения субъектов сохранит состояние субъекта. В случае ошибки при сохранении состояния объект данного субъекта отключается и активным становится новый экземпляр. 

Чтобы отменить регистрацию напоминания, субъект вызывает метод `UnregisterReminderAsync` (см. пример ниже).

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```

Как показано выше, метод `UnregisterReminderAsync` принимает интерфейс `IActorReminder`. Базовый класс субъекта поддерживает метод `GetReminder`, с помощью которого можно получить интерфейс `IActorReminder`, передав имя напоминания. Это удобно, поскольку субъекту не требуется сохранять интерфейс `IActorReminder`, который был возвращен вызовом метода `RegisterReminder`.

## <a name="next-steps"></a>Дальнейшие действия
* [События субъекта](service-fabric-reliable-actors-events.md)
* [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->



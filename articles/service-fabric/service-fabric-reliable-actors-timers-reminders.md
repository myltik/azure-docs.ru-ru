<properties
   pageTitle="Таймеры и напоминания надежных субъектов | Microsoft Azure"
   description="Общие сведения о таймерах и напоминаниях для надежных субъектов Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>


# Таймеры и напоминания субъекта
Субъекты могут планировать для себя периодические операции, регистрируя таймеры или напоминания. В этой статье показано, как использовать таймеры и напоминания, а также объясняются различия между ними.

## Таймеры субъектов
Таймеры субъекта обеспечивают простую оболочку для таймера .NET, чтобы методы обратного вызова учитывали гарантии пошагового параллелизма, предоставляемые средой выполнения субъектов.

Субъекты могут использовать методы `RegisterTimer` и `UnregisterTimer` в своем базовом классе для регистрации и отмены регистрации своих таймеров. В приведенном ниже примере показано использование интерфейсов API таймера. Эти интерфейсы API очень похожи на таймер .NET. В этом примере при срабатывании таймера среда выполнения субъектов вызовет метод `MoveObject`. Этот метод гарантированно учитывает пошаговый параллелизм. Это означает, что никакие другие методы субъектов или обратные вызовы таймеров или напоминаний не будут выполняться до завершения этого обратного вызова.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

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

## Напоминания для субъекта
Напоминания — это механизм для срабатывания постоянных обратных вызовов по субъекту в заданные моменты времени. Их функциональные возможности аналогичны таймерам. В отличие от таймеров, напоминания активируются при любых обстоятельствах, пока субъект явно не отменит их регистрацию или не удалит их. В частности, напоминания срабатывают независимо от отключения субъектов и отработки отказов, так как в среде выполнения Actors сохраняются сведения о напоминаниях субъекта.

Чтобы зарегистрировать напоминание, субъект вызывает метод `RegisterReminderAsync`, предоставленный в базовом классе, как показано в примере ниже.

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

В этом примере `"Pay cell phone bill"` — имя напоминания. Это строка, которую субъект использует для уникальной идентификации напоминания. `BitConverter.GetBytes(amountInDollars)` — это контекст, связанный с напоминанием. Он будет передан обратно субъекту в качестве аргумента обратного вызова напоминания, т. е. `IRemindable.ReceiveReminderAsync`.

Субъекты, использующие напоминания, должны реализовать интерфейс `IRemindable` (см. пример ниже).

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
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

При активации напоминания среда выполнения субъектов Reliable Actors вызовет метод субъекта `ReceiveReminderAsync`. Субъект может зарегистрировать несколько напоминаний, и метод `ReceiveReminderAsync` будет вызываться при активации любого из них. Субъект с помощью имени напоминания, переданного методу `ReceiveReminderAsync`, может выяснить, какое напоминание сработало.

После завершения вызова `ReceiveReminderAsync` среда выполнения субъектов сохраняет состояние субъекта. В случае ошибки при сохранении состояния объект данного субъекта отключается и активным становится новый экземпляр. Чтобы указать, что состояние не требуется сохранять по завершении обратного вызова напоминания, можно установить флаг `ActorReminderAttributes.ReadOnly` в параметре `attributes` при вызове метода `RegisterReminder` для создания напоминания.

Чтобы отменить регистрацию напоминания, субъект вызывает метод `UnregisterReminder` (см. пример ниже).

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Как показано выше, метод `UnregisterReminder` принимает интерфейс `IActorReminder`. Базовый класс субъекта поддерживает метод `GetReminder`, с помощью которого можно получить интерфейс `IActorReminder`, передав имя напоминания. Это удобно, поскольку субъекту не требуется сохранять интерфейс `IActorReminder`, который был возвращен вызовом метода `RegisterReminder`.

## Дальнейшие действия
 - [События субъекта](service-fabric-reliable-actors-events.md)
 - [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
 - [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
 - [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Пример кода](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0406_2016-->
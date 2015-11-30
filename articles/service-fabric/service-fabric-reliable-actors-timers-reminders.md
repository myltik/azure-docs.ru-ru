<properties
   pageTitle="Таймеры и напоминания надежных субъектов"
   description="Общие сведения о таймерах и напоминаниях для надежных субъектов Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# Таймеры субъектов
Таймеры субъектов обеспечивают простую оболочку вокруг таймера .NET, чтобы методы обратного вызова учитывали пошаговые гарантии параллелизма, предоставляемые средой выполнения субъектов.

Субъекты могут использовать методы `RegisterTimer` и `UnregisterTimer` в своем базовом классе для регистрации и отмены регистрации своих таймеров. В приведенном ниже примере показано использование интерфейсов API таймера. Эти интерфейсы API очень похожи на таймер .NET. В примере ниже при срабатывании таймера метод `MoveObject` будет вызван средой выполнения Actors с гарантированным учетом пошагового параллелизма, что означает, что никакие другие методы субъектов или обратные вызовы таймеров или напоминаний не будут выполняться до тех пор, пока не будет завершено выполнение этого обратного вызова.

```csharp
class VisualObjectActor : StatefulActor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

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

Среда выполнения Actors сохраняет состояние субъекта при завершении обратного вызова, если субъект поддерживает отслеживание состояния, как в приведенном выше примере. В случае ошибки при сохранении состояния объект данного субъекта отключается и активным становится новый экземпляр. Метод обратного вызова, который не изменяет состояние субъекта, можно зарегистрировать как обратный вызов таймера только для чтения, указав атрибут Readonly при обратном вызове таймера, как описано в разделе о [методах, доступных только для чтения](service-fabric-reliable-actors-introduction.md#readonly-methods).

При отключении субъекта в процессе сборки мусора все таймеры останавливаются. После этого обратные вызовы таймеров не выполняются. Кроме того, среда выполнения Actors не сохраняет никаких сведений о таймерах, запущенных до отключения. Регистрация таймеров, которые понадобятся субъекту при повторной активации в будущем, возлагается на субъект. Дополнительные сведения см. в статье [Сборка мусора и субъекты](service-fabric-reliable-actors-lifecycle.md).

## Напоминания для субъекта
Напоминания — это механизм для срабатывания постоянных обратных вызовов по субъекту в заданные моменты времени. Их функциональные возможности аналогичны таймерам, но в отличие от таймеров напоминания срабатывают при любых обстоятельствах до тех пор, пока регистрация напоминания не будет явно отменена субъектом. В частности, напоминания срабатывают независимо от отключения субъектов и отработки отказов, так как в среде выполнения Actors сохраняются сведения о напоминаниях субъекта.

Напоминания поддерживаются только для субъектов с отслеживанием состояния. Субъекты без отслеживания состояния не могут использовать напоминания. За хранение информации о напоминаниях, которые были зарегистрированы субъектами, отвечают поставщики состояний субъектов.

Чтобы зарегистрировать напоминание, субъект вызывает метод `RegisterReminder`, предоставленный в базовом классе (см. пример ниже).

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

В приведенном выше примере `"Pay cell phone bill"` — это имя напоминания. Это имя является строкой, которую субъект использует для уникальной идентификации напоминания. `BitConverter.GetBytes(amountInDollars)` — это контекст, связанный с напоминанием. Он будет передан обратно субъекту в качестве аргумента обратного вызова напоминания, т. е. `IRemindable.ReceiveReminderAsync`.

Субъекты, использующие напоминания, должны реализовать интерфейс `IRemindable` (см. пример ниже).

```csharp
public class ToDoListActor : StatefulActor<ToDoList>, IToDoListActor, IRemindable
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

При срабатывании напоминания среда выполнения Fabric Actors вызывает метод `ReceiveReminderAsync` субъекта. Субъект может зарегистрировать несколько напоминаний, и метод `ReceiveReminderAsync` будет вызываться в любое время при срабатывании любого из этих напоминаний. Субъект с помощью имени напоминания, переданного методу `ReceiveReminderAsync`, может выяснить, какое напоминание сработало.

После завершения вызова `ReceiveReminderAsync` среда выполнения Actors сохраняет состояние субъекта. В случае ошибки при сохранении состояния объект данного субъекта отключается и активным становится новый экземпляр. Чтобы указать, что состояние не требуется сохранять по завершении обратного вызова напоминания, можно установить флаг `ActorReminderAttributes.ReadOnly` в параметре `attributes` при вызове метода `RegisterReminder` для создания напоминания.

Чтобы отменить регистрацию напоминания, следует вызвать метод `UnregisterReminder` (см. пример ниже).

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Как показано выше, метод `UnregisterReminder` принимает интерфейс `IActorReminder`. Базовый класс субъекта поддерживает метод `GetReminder`, с помощью которого можно получить интерфейс `IActorReminder`, передав имя напоминания. Это удобно, поскольку субъекту не требуется сохранять интерфейс `IActorReminder`, который был возвращен вызовом метода `RegisterReminder`.

<!---HONumber=Nov15_HO4-->
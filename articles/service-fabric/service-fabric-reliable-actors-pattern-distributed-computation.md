<properties
   pageTitle="Шаблон распределенных вычислений | Microsoft Azure"
   description="Субъекты Reliable Actors Service Fabric отлично подходят для работы с параллельным асинхронным обменом сообщениями, параллельными вычислениями и для управления состояниями в распределенных средах."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Шаблон разработки Reliable Actors: распределенные вычисления
Этот шаблон возник отчасти благодаря реальной ситуации: наш клиент смог в рекордно короткие сроки выполнить финансовый расчет с помощью субъектов Reliable Actors, предоставляемых платформой Azure Service Fabric. Он рассчитал риски с помощью моделирования по методу Монте-Карло.

Без специальных знаний преимущества использования Service Fabric для обработки такого типа рабочей нагрузки вместо традиционных средств (таких как MapReduce или Message Passing Interface) могут показаться неочевидными.

Тем не менее практика показала, что эта платформа отлично подходит для выполнения параллельного асинхронного обмена сообщениями и параллельных вычислений, а также управления состояниями в распределенной среде (см. схему).

![Service Fabric: выполнение параллельного асинхронного обмена сообщениями и параллельных вычислений, а также управление состояниями в распределенных средах.][1]

В следующем примере мы просто вычислим число π, используя моделирование по методу Монте-Карло. У нас есть следующие субъекты.

* Обработчик. Отвечает за расчет π и использует субъекты пула задач.

* Пул задач. Отвечает за моделирование по методу Монте-Карло и отправку результатов агрегатору.

* Агрегатор. Отвечает за агрегирование результатов и отправку их финализатору.

* Финализатор. Отвечает за расчет конечного результата и его вывод на экран.

## Образец кода распределенных вычислений: моделирование по методу Монте-Карло

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : StatelessActor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : StatelessActor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Как правило, агрегирование результатов в Service Fabric осуществляется с помощью таймеров. Мы используем субъекты без отслеживания состояния преимущественно по двум причинам: среда выполнения динамически определит нужное количество агрегаторов и при необходимости обеспечит масштабирование. Кроме того, она создаст локальные экземпляры вызывающих субъектов. Другими словами, использование того же приемника команд, что и для вызывающего субъекта, позволяет минимизировать сетевые переходы.

Ниже показано, как выглядят агрегатор и финализатор.

## Образец кода распределенных вычислений: агрегатор

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : StatefulActor<AggregatorState>, IAggregator
{
    protected override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : StatefulActor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return Task.FromResult(true);
    }
}
```

На этом этапе уже должно быть понятно, как на примере с таблицей лидеров можно повысить производительность и масштабируемость, добавив агрегатор.

Мы ни в коем случае не утверждаем, что средства Service Fabric — это готовая замена другим решениям для высокопроизводительных или распределенных вычислений в системах обработки больших данных. Некоторые задачи платформа решает лучше, чем другие. Тем не менее это решение позволяет моделировать рабочие процессы и применять распределенные параллельные вычисления без каких-либо сложностей.

## Дальнейшие действия
[Конструктивный шаблон Reliable Actors: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Конструктивный шаблон Reliable Actors: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Конструктивный шаблон Reliable Actors: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Конструктивный шаблон Reliable Actors в Service Fabric: построение служб с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Конструктивный шаблон Reliable Actors: некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=AcomDC_0121_2016-->
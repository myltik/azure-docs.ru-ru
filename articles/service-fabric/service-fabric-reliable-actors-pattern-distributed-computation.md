<properties
   pageTitle="Шаблон разработки надежных субъектов: распределенные вычисления"
	description="Надежные субъекты Service Fabric отлично подходят для работы с параллельным асинхронным обменом сообщениями, параллельными вычислениями и для управления состояниями в распределенных средах."
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
	ms.author="claudioc"/>

# Шаблон разработки надежных субъектов: распределенные вычисления
Этот шаблон возник отчасти благодаря реальной ситуации: наш клиент смог в рекордно короткие сроки выполнить финансовый расчет с помощью надежных субъектов Service Fabric, а точнее рассчитать риски с помощью моделирования по методу Монте-Карло.

В первую очередь отметим, что, в отличие от традиционных моделей, таких как MapReduce или MPI, способность Azure Service Fabric решать такого рода задачи может быть неочевидна, особенно тем, у кого нет экспертных знаний в этой предметной области.

Тем не менее практика показала, что платформа Azure Service Fabric отлично подходит для работы с параллельным асинхронным обменом сообщениями, параллельными вычислениями и для управления состояниями в распределенной среде (см. диаграмму).

![][1]

В следующем примере мы просто вычислим число π, используя моделирование по методу Монте-Карло. У нас есть следующие субъекты:

* Обработчик. Отвечает за расчет π и использует субъекты PoolTask.

* PoolTask. Отвечает за моделирование по методу Монте-Карло и отправку результатов агрегатору.

* Агрегатор. Отвечает за агрегирование результатов и отправку их финализатору.

* Финализатор. Отвечает за расчет конечного результата и его вывод на экран.

## Образец кода распределенных вычислений: моделирование по методу Монте-Карло

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(0); // stateless
            tasks.Add(task.CalculateAsync(tries, seed));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed)
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

        var agg = ActorProxy.Create<IAggregator>(ActorId.NewId());
        return agg.AggregateAsync(pi);
    }
}
```

Как правило, агрегирование результатов в Azure Service Fabric осуществляется с помощью таймеров. Мы используем субъекты без отслеживания состояния преимущественно по двум причинам: среда выполнения динамически определит нужное количество агрегаторов и при необходимости обеспечит масштабирование. Кроме того, она создаст локальные экземпляры вызывающих субъектов, что уменьшит количество сетевых прыжков. Ниже показано, как выглядят агрегатор и финализатор.

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

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
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

public class Finaliser : Actor<FinalizerState>, IFinaliser
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

        return TaskDone.Done;
    }
}
```

На этом этапе уже должно быть понятно, как можно потенциально улучшить производительность и масштабируемость в примере с таблицей лидеров, добавив агрегатор.

Мы ни в коем случае не утверждаем, что Azure Service Fabric — это готовая замена другим решениям для высокопроизводительных или распределенных вычислений в системах обработки больших данных. Просто есть сценарии, для которых эта платформа подходит лучше других. Тем не менее платформа Azure Service Fabric позволяет моделировать рабочие процессы и применять распределенные параллельные вычисления, пользуясь такими преимуществами платформы, как простота и удобство.

## Дальнейшие действия
[Шаблон: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Шаблон: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Шаблон: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=September15_HO1-->
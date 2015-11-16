<properties
   pageTitle="Конструктивный шаблон смарт-кэша | Microsoft Azure"
   description="Конструктивный шаблон по использованию модели программирования Reliable Actors Service Fabric для создания инфраструктуры кэширования для веб-приложений."
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Шаблон проектирования надежных субъектов: смарт-кэш

Сочетание веб-уровня, уровня кэширования, уровня хранения и иногда уровня исполнителей — почти стандартные составляющие сегодняшних приложений. Уровень кэширования обычно критически важен для производительности и может сам состоять из нескольких уровней. Многие кэши являются простыми парами «ключ — значение», в то время как другие системы, используемые в качестве кэшей, например [Redis](http://redis.io), поддерживают более широкую семантику. Тем не менее любой специальный уровень кэширования будет ограничен в семантике и, что важнее, станет еще одним уровнем, которым необходимо управлять. Что если бы вместо этого объекты просто сохраняли состояние в локальных переменных и можно было бы сохранять моментальные снимки этих объектов или автоматически помещать в постоянное хранилище? Кроме того, расширенные коллекции, такие как списки, отсортированные наборы, очереди или любой другой пользовательский тип, по сути просто моделируются как переменные-члены и методы.

![][1]

## Образец таблицы лидеров

Рассмотрим в качестве примера таблицу лидеров. Объект Leaderboard должен поддерживать отсортированный список игроков и их очков, чтобы мы могли его запросить. Например, для получения списка «Ведущие 100 игроков» или для поиска позиции игрока в таблице лидеров относительно +-N игроков выше или ниже него. Для типичного решения с помощью традиционных средств потребуется запрос GET к объекту Leaderboard (коллекция, которая поддерживает вставку нового кортежа<Player  Points> с именем Score), его сортировка и, наконец, запрос PUT для помещения его обратно в кэш. Вероятно, для поддержания согласованности мы будем блокировать объект Leaderboard (запросы LOCK, GETLOCK, PUTLOCK). Давайте создадим решение на основе субъектов, где объединяются состояние и поведение. Существуют два варианта:

* реализовать коллекцию Leaderboard как часть субъекта,
* или использовать субъект как интерфейс к коллекции, которую мы можем хранить в переменной-члене. Сначала давайте рассмотрим, как может выглядеть интерфейс.

## Образец кода смарт-кэша — интерфейс Leaderboard

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

Далее мы реализуем этот интерфейс и воспользуемся вторым вариантом — инкапсулируем поведение данной коллекции в субъекте.

## Образец кода смарт-кэша — субъект Leaderboard

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

Член класса state предоставляет состояние субъекта. В образце кода выше он также предоставляет методы для чтения и записи данных.

## Образец кода смарт-кэша — LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

Никакой доставки данных, никакой блокировки — просто обработка удаленных объектов в распределенной среде выполнения с обслуживанием множества клиентов, как если бы они были отдельными объектами в одном приложении, обслуживающем только один клиент. Ниже приведен образец клиента.

## Образец кода смарт-кэша — вызов субъекта Leaderboard

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

Выходные данные выглядят следующим образом:

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Масштабирование архитектуры
Может показаться, что приведенный выше пример создаст узкое место в экземпляре Leaderboard. Что делать, если для экземпляра мы планируем поддержку сотен и тысяч игроков? Один из способов справиться с этим — внедрение агрегаторов без сохранения состояния, которые будут служить буфером, т. е. будут содержать неполные очки (скажем, промежуточные итоги) и затем периодически их отправлять субъекту Leaderboard, который может поддерживать окончательную таблицу лидеров. Мы обсудим подробнее этот метод «агрегации» позже. Кроме того, нам не требуется учитывать мьютексы, семафоры или другие конструкции для обеспечения параллелизма, которые обычно требуются для правильной работы параллельных программ. Ниже приведен еще один пример кэша, где демонстрируется расширенная семантика, которую можно реализовать с помощью субъектов. На этот раз мы реализуем логику очереди приоритетов (чем ниже число, тем выше приоритет) в рамках реализации субъекта. Интерфейс для IJobQueue выглядит примерно так:

## Образец кода смарт-кэша — интерфейс очереди заданий

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Также нам необходимо будет определить элемент задания — Job.

## Образец кода смарт-кэша — задание

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Наконец, мы реализуем интерфейс IJobQueue в деталях. Обратите внимание, что мы здесь для ясности опустили детали реализации очереди приоритета. Пример реализации можно найти в соответствующих образцах.

## Образец кода смарт-кэша — очередь заданий

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

Выходные данные выглядят следующим образом:

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Субъекты обеспечивают гибкость
В примерах выше (Leaderboard и JobQueue) мы использовали два различных способа.

* В примере с таблицей лидеров (Leaderboard) мы инкапсулировали объект Leaderboard как закрытую переменную-член в субъекте и просто предоставили интерфейс к этому объекту — к его состоянию и к функциональным возможностям.

* С другой стороны, в образце JobQueue мы реализовали субъект как саму очередь приоритета, вместо ссылки на другой объект, определенный в другом месте.

Субъекты предоставляют разработчику гибкость в определении расширенных структур объектов в рамках субъектов или в обращении к графам объектов за пределами субъектов. Во время кэширования субъекты могут выполнять запись с задержкой или сквозную запись либо можно использовать разные приемы при меняющейся степени детализации членов. Иными словами, у нас есть полный контроль над тем, что и когда сохраняется. Нам не нужно сохранять переходное состояние или состояние, которое мы можем построить из сохраненного. А как насчет заполнения этих кэшей субъектов? Это выполняется несколькими способами. Субъекты предоставляют виртуальные методы OnActivateAsync() и OnDectivateAsync(), позволяющие узнать, когда экземпляр субъекта активируется и деактивируется. Обратите внимание, что субъект активируется по требованию при отправке первого запроса к нему. Мы можем использовать OnActivateAsync() для заполнения состояния по требованию, как при сквозном чтении, возможно, из внешнего стабильного хранилища. Или мы можем заполнять состояние по таймеру, скажем, для субъекта обменного курса, который предоставляет функцию конвертации на основе последних курсов валют. Этот субъект может периодически заполнять свое состояние из внешней службы, например каждые 5 секунд, и использовать это состояние в функции преобразования. См. пример ниже.

## Образец кода смарт-кэша — конвертер курсов

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
}

```

По сути смарт-кэш обеспечивает следующие возможности.

* Высокую пропускную способность и низкую задержку благодаря запросам служб из памяти.
* Маршрутизацию одного экземпляра и однопоточную сериализацию запросов к элементу без возникновения конфликтов в постоянном хранилище.
* Семантические операции, например постановки в очередь (для элемента-задания).
* Простую в реализации сквозную запись или запись с задержкой.
* Автоматическое удаление данных о последних использованных элементах (LRU) (при управлении ресурсами).
* Автоматическую эластичность и надежность.


## Дальнейшие действия

[Шаблон: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Шаблон: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Шаблон: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=Nov15_HO2-->
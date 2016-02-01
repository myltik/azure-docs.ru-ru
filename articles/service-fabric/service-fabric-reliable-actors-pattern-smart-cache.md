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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Конструктивный шаблон Reliable Actors: смарт-кэш

Сочетание веб-уровня, уровня кэширования, уровня хранения и (иногда) уровня рабочих ролей — стандартные составляющие современных приложений. Уровень кэширования обычно критически важен для производительности и может сам состоять из нескольких уровней. Многие кэши являются простыми парами "ключ — значение". Другие системы, такие как [Redis](http://redis.io), используются в роли кэша, но они также поддерживают более широкую семантику. Но любой специальный уровень кэширования будет иметь семантические ограничения. Более того, это еще один уровень, которым нужно управлять.

Объекты могут сохранять состояние в локальных переменных; также можно создавать моментальные снимки этих объектов или автоматически помещать эти объекты в постоянное хранилище. Кроме того, расширенные коллекции, такие как списки, отсортированные наборы, очереди или любый другие пользовательские типы, просто моделируются как переменные-члены и методы.

![Субъекты и кэширование][1]

## Изучение примера "Список лидеров"

В качестве примера рассмотрим список лидеров. Объект "Список лидеров" должен поддерживать отсортированный список игроков и их оценок, чтобы мы могли его запросить. Запрос может найти 100 лучших игроков. Также можно найти позицию игрока в списке лидеров относительно указанного числа игроков выше и ниже его. Для традиционного решения потребуется запрос GET к объекту "Список лидеров" (коллекция, которая поддерживает вставку нового кортежа `<Player, Points>` с именем **Score**), сортировка запроса и запрос PUT для помещения его обратно в кэш. Вероятно, для обеспечения согласованности потребуется заблокировать объект "Список лидеров" (запросы GETLOCK, PUTLOCK). Ознакомимся с решением на основе субъектов, в котором объединяются состояние и поведение. Существуют два варианта:

* реализовать коллекцию "Список лидеров" как часть субъекта;
* использовать субъект как интерфейс к коллекции, которую мы можем хранить в переменной-члене.

В следующем примере кода показано, как может выглядеть интерфейс.

### Пример кода смарт-кэша — интерфейс Leaderboard

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

Далее вы можете реализовать этот интерфейс, используя второй вариант, и инкапсулировать поведение данной коллекции в субъект.

### Пример кода смарт-кэша — субъект Leaderboard

```
public class Leaderboard : StatefulActor<LeaderboardCollection>, ILeaderboard
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

Член состояния класса указывает состояние субъекта. В примере кода выше он также указывает методы для чтения и записи данных.

### Пример кода смарт-кэша — LeaderboardCollection

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

В этом подходе мы не применяем ни доставку данных, ни блокировку. Это просто обработка удаленных объектов в распределенной среде выполнения с обслуживанием множества клиентов, как если бы они были отдельными объектами в одном приложении, обслуживающем только один клиент. В следующем примере кода рассматривается пример клиента.

### Пример кода смарт-кэша — вызов субъекта Leaderboard

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
Может показаться, что приведенный выше пример создаст проблемы в экземпляре Leaderboard. Например, если вы планируете поддерживать тысячи игроков. Одно из решений — ввести агрегаторы без отслеживания состояния, которые действуют как буферы. Эти агрегаторы будут содержать неполные (промежуточные) результаты соревнований, а затем периодически отправлять их субъекту Leaderboard, который будет поддерживать окончательный список лидеров. Подробнее мы обсудим эту технику позже. Кроме того, нам не нужно учитывать мьютексы, семафоры или другие конструкции для обеспечения параллелизма, которые обычно требуются для правильного поведения параллельных программ.

Ниже приведен еще один пример кэша, где демонстрируется расширенная семантика, которую можно реализовать с помощью субъектов. На этот раз мы реализуем логику очереди приоритетов (чем ниже число, тем выше приоритет) в рамках реализации субъекта. В следующем примере кода представлен обзор интерфейса для **IJobQueue**.

### Пример кода смарт-кэша — интерфейс очереди заданий

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Также нам необходимо будет определить элемент **Job**.

### Пример кода смарт-кэша — задание

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

Наконец, мы реализуем интерфейс IJobQueue в субъекте. Обратите внимание, что мы здесь для ясности опустили детали реализации очереди приоритета. Реализацию можно рассмотреть на соответствующих примерах.

### Пример кода смарт-кэша — очередь заданий

```
public class JobQueue : StatefulActor<List<Jobs>>, IJobQueue
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

## Использование субъектов для обеспечения гибкости
В примерах выше (Leaderboard и JobQueue) мы использовали две разные техники.

* В примере со списком лидеров мы инкапсулировали объект Leaderboard как закрытую переменную-член в субъекте. Затем мы просто предоставили интерфейс для этого объекта — как для его функциональных возможностей, так и для состояния.

* В примере JobQueue мы реализовали субъект как саму очередь приоритетов, вместо ссылки на другой объект, определенный в другом месте.

Субъекты предоставляют разработчику гибкость при определении расширенных структур объектов в рамках субъектов или в обращении к графам объектов за пределами субъектов. Во время кэширования субъекты могут выполнять запись с задержкой или сквозную запись, а также использовать разные приемы при меняющейся степени детализации переменных-членов. У вас есть полный контроль над тем, что и когда сохраняется. Вам не нужно сохранять переходное состояние или состояние, которое вы можете построить из сохраненного.

Как заполняются кэши этих субъектов? Это выполняется несколькими способами. Субъекты предоставляют виртуальные методы **OnActivateAsync()** и **OnDeactivateAsync()**, позволяющие узнать, когда экземпляр субъекта активируется и деактивируется. Обратите внимание, что субъект активируется по требованию при отправке первого запроса к нему.

Вы можете использовать OnActivateAsync() для заполнения состояния по требованию, как при сквозном чтении из внешнего стабильного хранилища. Также вы можете заполнять состояние по таймеру, скажем, для субъекта обменного курса, который предоставляет функцию конвертации на основе последних курсов валют. Этот субъект может периодически заполнять свое состояние из внешней службы (например, каждые 5 секунд) и использовать это состояние в функции конвертации. В следующем примере кода показано, как это сделать.

### Пример кода смарт-кэша — конвертер курсов

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

Подход смарт-кэша предоставляет следующие преимущества.

* Высокую пропускную способность и низкую задержку благодаря запросам служб из памяти.
* Маршрутизация одного экземпляра и однопоточная сериализация запросов к элементу без состязания в постоянном хранилище.
* Семантические операции, например **Enqueue(Job item)**.
* Простая в реализации сквозная запись или запись с задержкой.
* Автоматическое вытеснение данных о последних использованных элементах (LRU) (при управлении ресурсами).
* Автоматическую эластичность и надежность.


## Дальнейшие действия

[Конструктивный шаблон Reliable Actors: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Конструктивный шаблон Reliable Actors: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Конструктивный шаблон Reliable Actors в Service Fabric: построение служб с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Конструктивный шаблон Reliable Actors: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Конструктивный шаблон Reliable Actors: некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=AcomDC_0121_2016-->
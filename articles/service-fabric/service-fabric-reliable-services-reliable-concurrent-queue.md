---
title: Надежная параллельная очередь в Azure Service Fabric
description: Надежная параллельная очередь — это очередь с высокой пропускной способностью, что позволяет параллельно ставить в очередь и выводить из нее.
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: e04123f7870921a2979564d0f6c68424d4d7711c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206583"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Введение в надежные параллельные очереди в Azure Service Fabric
Надежная параллельная очередь — это асинхронная, транзакционная и реплицируемая очередь, которая обладает высокой степенью параллелизма при операциях постановки в очередь и вывода из нее. Она предназначена для обеспечения высокой пропускной способности и низкой задержки. Она ослабляет строгое упорядочение FIFO, гарантируемое [надежной очередью](https://msdn.microsoft.com/library/azure/dn971527.aspx), и вместо этого обеспечивает упорядочение наилучшим возможным образом.

## <a name="apis"></a>Интерфейсы API

|Параллельная очередь                |Надежная параллельная очередь                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Сравнение с [надежной очередью](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Надежная параллельная очередь предлагается в качестве альтернативы [надежной очереди](https://msdn.microsoft.com/library/azure/dn971527.aspx). Ее следует использовать в случаях, когда строгое упорядочение FIFO не требуется, так как модель FIFO требует уменьшения степени параллелизма.  [Надежная очередь](https://msdn.microsoft.com/library/azure/dn971527.aspx) использует блокировки для принудительного упорядочивания FIFO, разрешая ставить в очередь и выводить из нее одновременно максимум одну транзакцию за раз. Для сравнения надежная параллельная очередь снижает ограничения упорядочивания и позволяет чередовать операции постановки в очередь и вывода из нее любому количеству параллельных транзакций. Так обеспечивается упорядочивание наилучшим возможным образом, однако относительный порядок двух значений в надежной параллельной очереди не гарантируется.

Надежная параллельная очередь обеспечивает высокую пропускную способность и более низкую задержку, чем [надежная очередь](https://msdn.microsoft.com/library/azure/dn971527.aspx), во всех случаях, когда имеется несколько параллельных транзакций, выполняющих постановку в очередь и вывод из нее.

Пример использования надежной параллельной очереди — это сценарий с [очередью сообщений](https://en.wikipedia.org/wiki/Message_queue). В этом сценарии один или несколько отправителей создают и добавляют элементы в очередь и один или несколько получателей извлекают сообщения из очереди и обрабатывают их. Несколько отправителей и получателей могут работать независимо, используя параллельные транзакции для обработки очередей.

## <a name="usage-guidelines"></a>Рекомендации по использованию
* Очередь предполагает, что элементы в ней имеют низкий период удержания. То есть элементы не будут оставаться в очереди в течение длительного времени.
* Очередь не гарантирует строгий порядок FIFO.
* Очередь не считывает свои собственные записи. Если элемент поставлен в очередь внутри транзакции, он не будет виден для средства вывода из очереди внутри той же транзакции.
* Операции вывода из очереди не изолированы друг от друга. Если элемент *A* выведен из очереди в транзакции *txnA*, тогда как *txnA* не зафиксирована, элемент *A* не будет виден параллельной транзакции *txnB*.  Если транзакция *txnA* прервется, элемент *A* немедленно станет видимым для транзакции *txnB*.
* Поведение *TryPeekAsync* может быть реализовано с использонием метода *TryDequeueAsync* с последующим прерыванием транзакции. Пример можно найти в разделе "Шаблоны программирования".
* Число является нетранзакционным. Это число позволяет получить представление о количестве элементов в очереди, но так как оно представляет данные на определенный момент времени, на него нельзя положиться.
* Ресурсоемкая обработка выведенных из очереди элементов не должна выполняться во время транзакции, чтобы избежать продолжительных транзакций, которые могут оказывать влияние на работу системы.

## <a name="code-snippets"></a>Фрагменты кода
Рассмотрим несколько фрагментов кода и ожидаемые выходные данные. Обработка исключений не рассматривается в этом разделе.

### <a name="enqueueasync"></a>EnqueueAsync
Вот несколько фрагментов кода по использованию EnqueueAsync, после которых приведены ожидаемые выходные данные.

- *Вариант 1. Одно задание постановки в очередь*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Предположим, что задача выполнена успешно и одновременные транзакции, изменяющие очередь, отсутствовали. Очередь может содержать элементы в любом из следующих порядков:

> 10, 20

> 20, 10


- *Вариант 2. Параллельное задание постановки в очередь*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Предположим, что задачи успешно завершены, они выполнялись параллельно и не было других одновременных транзакций, изменяющих очередь. Невозможно сделать вывод о порядке элементов в очереди. В этом фрагменте кода элементы могут отображаться в любом из 4 возможных порядков.  Очередь попытается разместить элементы в первоначальном порядке (постановки в очередь), но может быть вынуждена переупорядочить их из-за одновременных операций или сбоев.


### <a name="dequeueasync"></a>DequeueAsync
Вот несколько фрагментов кода по использованию метода TryDequeueAsync, за которыми следуют ожидаемые выходные данные. Предположим, что очередь уже заполнена следующими элементами:
> 10, 20, 30, 40, 50, 60

- *Вариант 1. Одно задание вывода из очереди*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Предположим, что задача выполнена успешно и одновременные транзакции, изменяющие очередь, отсутствовали. Так как нельзя сделать вывод о порядке элементов в очереди, любые три элемента могут быть выведены из нее в любом порядке. Очередь попытается разместить элементы в первоначальном порядке (постановки в очередь), но может быть вынуждена переупорядочить их из-за одновременных операций или сбоев.  

- *Вариант 2. Параллельное задание вывода из очереди*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Предположим, что задачи успешно завершены, они выполнялись параллельно и не было других одновременных транзакций, изменяющих очередь. Так как невозможно сделать вывод о порядке элементов в очереди, списки *dequeue1* и *dequeue2* будут содержать любые два элемента в любом порядке.

Один и тот же элемент *не будет содержаться* в обоих списках. Поэтому если dequeue1 имеет элемент *10*, *30*, то deque2 будет иметь элементы *20*, *40*.

- *Вариант 3. Упорядочение вывода из очереди с прерыванием транзакции*

Прерывание транзакции во время вывода из очереди возвращает элементы обратно в начало очереди. Порядок, в котором элементы возвращаются в начало очереди, не гарантируется. Рассмотрим следующий пример:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Предположим, что элементы выводились из очереди в следующем порядке:
> 10, 20

Когда мы прервем транзакцию, элементы будут добавлены обратно в начало очереди в одном из следующих порядков:
> 10, 20

> 20, 10

То же самое верно для всех случаев, когда транзакция не была успешно *зафиксирована*.

## <a name="programming-patterns"></a>Шаблоны программирования
В этом разделе мы рассмотрим несколько шаблонов программирования, которые могут быть полезны при использовании надежной параллельной очереди.

### <a name="batch-dequeues"></a>Вывод из очереди в пакетном режиме
Этот шаблон программирования подразумевает, что задача получателя выполняет вывод из очереди в пакетном режиме, вместо того чтобы выполнять один вывод за раз. Пользователь может регулировать задержки между пакетами и размеры пакетов. В следующем фрагменте кода показана эта модель программирования.  Обратите внимание, что в этом примере обработка выполняется после фиксирования транзакции, поэтому, если во время обработки произойдет ошибка, необработанные элементы будут потеряны.  Кроме того, обработка может быть выполнена в пределах области транзакции, однако это может отрицательно сказаться на производительности и требует обработки уже обработанных элементов.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Оптимальная обработка на основе уведомлений
Другой интересный шаблон программирования использует API подсчета. Здесь мы можем реализовать обработку на основе уведомлений для очереди. Число элементов в очереди может использоваться для регулирования заданий постановки в очередь и вывода из нее.  Обратите внимание, что, как и в предыдущем примере, так как обработка происходит за пределами транзакции, необработанные элементы могут быть потеряны, если во время обработки возникнет ошибка.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Оптимальная очистка
Очистка очереди не гарантируется из-за свойства параллельности структуры данных.  Вполне возможно, что даже если никакие пользовательские операции в очереди не выполняются, конкретный вызов метода TryDequeueAsync не сможет вернуть элемент, который ранее был поставлен в очередь и зафиксирован.  Поставленный в очередь элемент *в конечном счете* станет видимым для операции вывода из очереди, но без внешнего механизма обмена данными независимый получатель не узнает, что очередь достигла устойчивого состояния, даже если все процедуры были остановлены, а новые операции ввода в очередь запрещены. Таким образом операция очистки выполняется наилучшим возможным образом, как показано ниже.

Прежде чем попытаться очистить очередь, пользователь должен остановить все последующие задачи отправителя и получателя и ожидать подтверждения или прерывания любых выполняемых транзакций.  Если пользователь знает предполагаемое количество элементов в очереди, он может настроить уведомление, которое сигнализирует о том, что все элементы выведены из очереди.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Обзор
Надежная параллельная очередь не предоставляет API *TryPeekAsync*. Пользователи могут получить семантику обзора с помощью метода *TryDequeueAsync* и прерывания транзакции. В этом примере вывод из очереди обрабатывается только в том случае, если значение элемента больше чем *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Дополнительные сведения
* [Приступая к работе с надежными службами](service-fabric-reliable-services-quick-start.md)
* [Работа с Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Уведомления Reliable Services](service-fabric-reliable-services-notifications.md)
* [Резервное копирование и восстановление служб Reliable Services и субъектов Reliable Actors](service-fabric-reliable-services-backup-restore.md)
* [Настройка надежных служб с отслеживанием состояния](service-fabric-reliable-services-configuration.md)
* [ASP.NET Core в Service Fabric Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Дополнительные возможности использования модели программирования надежных служб](service-fabric-reliable-services-advanced-usage.md)
* [Справочник разработчика по надежным коллекциям](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

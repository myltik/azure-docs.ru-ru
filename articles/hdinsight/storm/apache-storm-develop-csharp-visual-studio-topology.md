---
title: Разработка топологий Apache Storm с помощью Visual Studio и C# — Azure HDInsight | Документы Майкрософт
description: Сведения о создании топологий Storm в C#. Создайте простую топологию статистики в Visual Studio с помощью средств Hadoop для Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: ''
ms.devlang: java
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 1e35d795fb65c837f7a4152920f701da5bf8f506
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Разработка топологий для Apache Storm на C# с помощью средств Data Lake для Visual Studio

Сведения о создании топологии Storm на языке C# с помощью средств Azure Data Lake (Hadoop) для Visual Studio. В этом документе приведены пошаговые инструкции по созданию проекта Storm в Visual Studio, его локальному тестированию и развертыванию в Apache Storm в кластере Azure HDInsight.

Вы также узнаете о том, как создавать гибридные топологии, использующие компоненты C# и Java.

> [!NOTE]
> Хотя действия, описанные в этой статье, зависят от среды разработки Windows и Visual Studio, скомпилированный проект можно отправить в кластер HDInsight под управлением Windows или Linux. Топологии SCP.NET поддерживаются только теми кластерами под управлением Linux, которые созданы после 28 октября 2016 года.

Чтобы использовать топологию C# с кластером под управлением Linux, обновите пакет NuGet Microsoft.SCP.Net.SDK, использующийся в проекте, до версии 0.10.0.6 или более поздней. Версия пакета также должна соответствовать основной версии Storm, установленной на HDInsight.

| Версия HDInsight | Версия Storm | Версия SCP.NET | Версия Mono по умолчанию |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(только в HDInsight под управлением Windows) | Нет данных |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> Для топологии C# на кластерах под управлением Linux используется .NET 4.5. В кластере HDInsight они запускаются с помощью Mono. Чтобы определить потенциальные проблемы с совместимостью, просмотрите документ о [совместимости Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="install-visual-studio"></a>Установка Visual Studio

Для разработки топологий C# с помощью SCP.NET можно использовать одну из следующих версий Visual Studio:

* Visual Studio 2012 с [обновлением 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 с [обновлением 4](http://www.microsoft.com/download/details.aspx?id=44921) или [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 или [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (любой выпуск).

## <a name="install-data-lake-tools-for-visual-studio"></a>Установка средств Data Lake для Visual Studio

Инструкции по установке средств Data Lake для Visual Studio см. в статье [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Установка Java

При отправке топологии Storm из Visual Studio SCP.NET создает ZIP-файл, содержащий топологию и зависимости. Эти ZIP-файлы создаются с помощью Java, так как при этом используется формат, который более совместим с кластерами под управлением Linux.

1. Установите Java Developer Kit (JDK) 7 или более поздней версии в среде разработки. Получить пакет Oracle JDK можно на сайте [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Вы также можете использовать [другие дистрибутивы Java](http://openjdk.java.net/).

2. Переменная среды `JAVA_HOME` должна указывать на каталог, содержащий Java.

3. Переменная среды `PATH` должна включать в себя каталог `%JAVA_HOME%\bin`.

Для проверки правильности установки Java и пакета JDK можно использовать следующее консольное приложение C#:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Шаблоны Storm

Средства Data Lake для Visual Studio предоставляют следующие шаблоны:

| Тип проекта | Что демонстрирует |
| --- | --- |
| Приложение Storm |Пустой проект топологии Storm |
| Пример модуля записи Storm Azure SQL |Запись в базу данных SQL Azure. |
| Пример модуля чтения Storm Azure Cosmos DB |Чтение из базы данных Azure Cosmos DB. |
| Пример модуля записи Storm Azure Cosmos DB |Запись в базу данных Azure Cosmos DB. |
| Пример модуля чтения концентратора событий Storm |Чтение из концентраторов событий Azure. |
| Пример модуля записи концентратора событий Storm |Запись в концентраторы событий Azure. |
| Пример модуля чтения Storm HBase |Чтение из HBase в кластерах HDInsight. |
| Пример модуля записи Storm HBase |Запись в HBase в кластерах HDInsight. |
| Пример Storm Hybrid |Использование компонентов Java. |
| Пример Storm |Базовая топология подсчета слов. |

> [!WARNING]
> Не все шаблоны работают с HDInsight под управлением Linux. Пакеты NuGet, используемые в шаблонах, могут оказаться несовместимыми с Mono. Проверьте документацию по [совместимости с Mono](http://www.mono-project.com/docs/about-mono/compatibility/) и используйте [анализатор переносимости .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis), чтобы определить возможные проблемы.

На данном этапе в этом документе используется базовый тип проекта приложения Storm для создания топологии.

### <a name="hbase-templates-notes"></a>Заметки о шаблонах HBase

Для взаимодействия с HBase в кластере HDInsight шаблоны модулей чтения и записи HBase используют REST API HBase, а не API Java HBase.

### <a name="eventhub-templates-notes"></a>Заметки о шаблонах EventHub

> [!IMPORTANT]
> Компонент spout EventHub на основе Java, входящий в состав шаблона EventHub Reader, не работает со Storm в HDInsight версии 3.5 или более поздней. Обновленная версия этого компонента доступна в [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Пример топологии, которая использует этот компонент и работает со Storm в HDInsight 3.5, см. в [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Создание топологии на C#

1. Откройте Visual Studio, выберите **Файл** > **Создать**, а затем — **Проект**.

2. В окне **Новый проект** разверните **Установленные** > **Шаблоны** и выберите **Azure Data Lake**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна введите имя приложения **WordCount** .

    ![Снимок экрана: окно "Новый проект"](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. После создания проекта у вас должны быть следующие файлы:

   * **Program.cs** — определяет топологию для проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одного объекта spout и одного объекта bolt.

   * **Spout.cs**— пример воронки, выдающей случайные числа.

   * **Bolt.cs**— пример сита, которое подсчитывает числа, созданные воронкой.

     При создании проекта NuGet скачивает последнюю версию [пакета SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Реализация воронки

1. Откройте файл **Spout.cs**. Воронки используются для считывания данных в топологии из внешнего источника. Ниже перечислены основные компоненты воронки.

   * **NextTuple**(Следующий кортеж) — вызывается Storm, когда воронке разрешено создавать новые кортежи.

   * **Ack** (Подтверждение, только для транзакционной топологии) — обрабатывает подтверждения, инициированные другими компонентами топологии для кортежей, отправленных из этой воронки. Подтверждение кортежа позволяет воронке определить, что он был успешно обработан нижестоящими компонентами.

   * **Fail** (Сбой, только для транзакционной топологии) — обрабатывает кортежи, обработка которых другими компонентами топологии завершилась сбоем. Реализация метода Fail дает возможность повторно создать кортеж для повторной обработки.

2. Замените содержимое класса **Spout** следующим текстом: This spout randomly emits a sentence into the topology (Этот элемент spout случайным образом выдает предложение в топологию).

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Реализация сит

1. Удалите существующий файл **Bolt.cs** из проекта.

2. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Добавить** > **Новый элемент**. Выберите в списке **Storm Bolt** (Сито воронки) и введите имя **Splitter.cs**. Повторите этот процесс для создания второго сита с именем **Counter.cs**.

   * **Splitter.cs** — реализует элемент bolt, который делит предложения на отдельные слова и выдает новый поток слов.

   * **Counter.cs** — реализует элемент bolt, который подсчитывает каждое слово и выдает новый поток слов и частоту их употребления.

     > [!NOTE]
     > Хотя эти сита считывают данные и записывают их в потоки, их можно также использовать для взаимодействия с базой данных, службой и т. д.

3. Откройте файл **Splitter.cs**. Он содержит только один метод по умолчанию — **Execute**. Этот метод вызывается, когда сито получает кортеж для обработки. Здесь можно считывать и обрабатывать входящие кортежи и создавать исходящие кортежи.

4. Замените содержимое файла класса **Splitter** следующим кодом:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Откройте файл **Counter.cs** и замените содержимое класса следующим кодом:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Определение топологии

Потоки данных между компонентами могут быть представлены в виде диаграммы воронок и сит. Граф для этой топологии выглядит следующим образом.

![Схема компонентов](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Предложения отправляются из компонента spout и распределяются между экземплярами элемента bolt Splitter. Сито Splitter разбивает предложения на слова, которые распределяются среди экземпляров сита Counter.

Так как статистика хранится локально в экземпляре Counter, убедитесь, что определенные слова поступают в один и тот же экземпляр сита Counter. Каждое конкретное слово отслеживается только одним экземпляром. Так как сито Splitter не поддерживает состояние, на самом деле неважно, какое предложение получает то или иное сито.

Откройте файл **Program.cs**. **GetTopologyBuilder** — важный метод, который используется, чтобы определить топологию, отправляющуюся в Storm. Чтобы реализовать описанную выше топологию, замените содержимое файла **GetTopologyBuilder** следующим кодом:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Отправка топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Submit to Storm on HDInsight** (Отправить в Storm в HDInsight).

   > [!NOTE]
   > При появлении запроса введите учетные данные для своей подписки Azure. Если у вас несколько подписок, воспользуйтесь той, что содержит ваш кластер Storm в HDInsight.

2. Выберите Storm в кластере HDInsight из раскрывающегося списка **Кластер Storm** и щелкните **Отправить**. Вы можете отслеживать выполнение отправки в окне **Выходные данные** .

3. После успешной отправки топологии должны появиться **топологии Storm** для кластера. Выберите топологию **WordCount** в списке, чтобы просмотреть информацию о выполняемой топологии.

   > [!NOTE]
   > **Топологии Storm** можно также просмотреть в **обозревателе сервера**. Разверните узлы **Azure** > **HDInsight**, щелкните правой кнопкой мыши элемент "Storm" в кластере HDInsight и выберите пункт **Просмотреть топологии Storm**.

    Чтобы просмотреть сведения о компонентах в топологии, дважды щелкните компонент на схеме.

4. В представлении **Topology Summary** (Сводка по топологии) выберите **Kill** (Удалить), чтобы остановить топологию.

   > [!NOTE]
   > Топологии Storm выполняются, пока не будут удалены или пока не будет удален кластер.

## <a name="transactional-topology"></a>Транзакционная топология

Предыдущая топология является нетранзакционной. Компоненты топологии не реализуют функциональные возможности для воспроизведения сообщений. Чтобы получить пример транзакционной топологии, создайте проект и выберите в качестве типа проекта **Пример Storm**.

Транзакционные топологии реализуют следующие возможности, обеспечивающие воспроизведение данных:

* **Кэширование метаданных** — в компоненте spout должны храниться метаданные для отправляемых данных, чтобы их можно было извлечь и отправить повторно в случае сбоя. Так как в этом примере отправляются данные небольшого размера, необработанные данные каждого кортежа хранятся в словаре для воспроизведения.

* **Подтверждение** — каждый элемент bolt в топологии может вызвать `this.ctx.Ack(tuple)`, чтобы подтвердить успешную обработку кортежа. Как только все сита подтвердят обработку кортежа, для воронки вызывается метод `Ack` . Метод `Ack` позволяет воронке удалить кэшированные данные для воспроизведения.

* **Fail** (Сбой) — каждое сито может вызвать `this.ctx.Fail(tuple)`, указав тем самым на сбой при обработке кортежа. Информация о сбое передается в метод `Fail` воронки, в которой кортеж может быть воспроизведен с помощью кэшированных метаданных.

* **Sequence ID**(Идентификатор последовательности) — при отправке кортежа может быть указан уникальный идентификатор последовательности. Это значение, по которому кортеж можно идентифицировать для обработки воспроизведения (подтверждения и сбоя). Например, воронка в проекте **Storm Sample** (Пример Storm) при отправке данных использует следующий код:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    При этом в заданный по умолчанию поток отправляется кортеж, содержащий предложение. Значение идентификатора последовательности содержится в **lastSeqId**. В этом примере **lastSeqId** увеличивается после каждой отправки кортежа.

Как показано в проекте **Пример Storm**, транзакционность компонента можно задать во время выполнения в соответствии с конфигурацией.

## <a name="hybrid-topology-with-c-and-java"></a>Гибридная топология с помощью C# и Java

Средства Data Lake для Visual Studio можно также использовать для создания гибридных топологий, в которых одни компоненты написаны на C#, а другие — на Java.

Чтобы получить пример гибридной топологии, создайте проект и выберите **Пример гибридного решения Storm**. В примере демонстрируются следующие понятия:

* **Компонент spout на Java** и **элемент bolt на C#** — определены в **HybridTopology_javaSpout_csharpBolt**.

    * Транзакционная версия определена в **HybridTopologyTx_javaSpout_csharpBolt**.

* **Компонент spout на C#** и **элемент bolt на Java** — определены в **HybridTopology_csharpSpout_javaBolt**.

    * Транзакционная версия определена в **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > В этой версии также демонстрируется использование кода Clojure из текстового файла в качестве компонента Java.


Чтобы изменить топологию, используемую при отправке проекта, перед отправкой в кластер переместите оператор `[Active(true)]` в топологию, которую необходимо использовать.

> [!NOTE]
> Все необходимые файлы Java предоставляются в составе этого проекта и находятся в папке **JavaDependency** .

При создании и отправке гибридной топологии учитывайте указанные ниже моменты.

* Для создания экземпляра класса Java для элемента spout или bolt используйте **JavaComponentConstructor**.

* Для сериализации данных в компоненты Java и из них из объектов Java в JSON используйте **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer**.

* Отправляя топологию на сервер, необходимо использовать параметр **Дополнительные конфигурации**, чтобы задать **пути к файлам Java**. При этом следует указать путь к каталогу, содержащему JAR-файлы с классами Java.

### <a name="azure-event-hubs"></a>Концентраторы событий Azure

В SCP.NET версии 0.9.4.203 появились новые класс и метод, предназначенные специально для работы с компонентом spout концентратора событий (компонентом spout Java, который считывает данные из концентраторов событий). При создании топологии, которая использует компонент spout концентратора событий, используйте указанные ниже методы.

* Класс **EventHubSpoutConfig**. Создает объект, который содержит настройки для компонента spout.

* Метод **TopologyBuilder.SetEventHubSpout**. Добавляет компонент spout концентратора событий к топологии.

> [!NOTE]
> Для сериализации данных, созданных компонентом spout, необходимо по-прежнему использовать **CustomizedInteropJSONSerializer**.

## <a id="configurationmanager"></a>Использование ConfigurationManager

Не используйте **ConfigurationManager**, чтобы получать значения конфигурации из компонентов bolt и spout. Это вызовет исключение пустого указателя. Вместо этого конфигурация проекта передается в топологию Storm как пара "ключ — значение" в контексте топологии. Каждому компоненту, зависящему от значений конфигурации, необходимо получить их из контекста во время инициализации.

В приведенном ниже коде показано, как получить эти значения.

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Если используется метод `Get`, чтобы вернуть экземпляр компонента, он должен передавать конструктору параметры `Context` и `Dictionary<string, Object>`. В примере ниже показан базовый метод `Get`, передающий значения должным образом.

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Обновление SCP.NET

Последние версии SCP.NET поддерживают обновление пакета через NuGet. Если доступно новое обновление, вы получите уведомление об обновлении. Чтобы вручную проверить обновления, выполните указанные ниже действия.

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.

2. Из диспетчера пакетов выберите **Обновления**. Если обновление доступно, оно будет отображено. Нажмите кнопку **Обновление** для его установки.

> [!IMPORTANT]
> Если проект был создан с помощью предыдущей версии SCP.NET, в которой не использовалось средство NuGet, выполните следующие действия для обновления до новой версии.
>
> 1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.
> 2. С помощью поля **Поиск** найдите **пакет SDK Microsoft.SCP.Net.** и добавьте его в проект.

## <a name="troubleshoot-common-issues-with-topologies"></a>Устранение распространенных неполадок с топологиями

### <a name="null-pointer-exceptions"></a>Исключение пустого указателя

При использовании топологии C# с кластером HDInsight под управлением Linux компоненты bolt и spout, считывающие параметры конфигурации с помощью **ConfigurationManager** во время выполнения, могут вернуть исключение пустого указателя.

Конфигурация проекта передается в топологию Storm как пара "ключ —значение" в контексте топологии. Ее можно извлечь из объекта dictionary, который передается в компоненты при их инициализации.

Дополнительные сведения см. в этой статье в разделе [ConfigurationManager](#configurationmanager).

### <a name="systemtypeloadexception"></a>System.TypeLoadException

При использовании топологии C# с кластером HDInsight под управлением Linux может возникнуть следующая ошибка:

    System.TypeLoadException: Failure has occurred while loading a type.

Она происходит, когда используется двоичный файл, несовместимый с версией .NET, поддерживающей Mono.

Для кластеров HDInsight под управлением Linux в проекте должны использоваться двоичные файлы, скомпилированные для .NET 4.5.

### <a name="test-a-topology-locally"></a>Локальная проверка топологии

Хотя топологию легко развернуть в кластер, иногда ее необходимо проверять локально. Чтобы запустить и протестировать пример топологии, описанной в этой статье, локально в среде разработки, сделайте следующее.

> [!WARNING]
> Локальное тестирование работает только для базовых топологий на C#. Нельзя использовать локальное тестирование для гибридных топологий или топологий, использующих несколько потоков.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Свойства**. В свойствах проекта измените **Тип выходных данных** на **Консольное приложение**.

    ![Снимок экрана: свойства проекта с выделенным свойством "Тип выходных данных"](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Перед развертыванием топологии в кластере не забудьте вернуть для параметра **Тип выходных данных** значение **Библиотеки классов**.

2. В **обозревателе решений** щелкните правой кнопкой мыши проект, а затем выберите **Добавить** > **Новый элемент**. Щелкните **Класс** и введите имя класса **LocalTest.cs**. Теперь нажмите кнопку **Добавить**.

3. Откройте класс **LocalTest.cs** и добавьте в начало следующий оператор **using**:

    ```csharp
    using Microsoft.SCP;
    ```

4. Вставьте в класс **LocalTest** следующий код:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Ознакомьтесь с комментариями в коде. В этом коде для запуска компонентов в среде разработки используется **LocalContext**. При этом поток данных сохраняется между компонентами текстовых файлов на локальном диске.

1. Откройте **Program.cs** и добавьте в метод **Main** следующий код:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Сохраните изменения, а затем нажмите клавишу **F5** или щелкните **Отладка** > **Начать отладку**, чтобы запустить проект. Должно появится окно консоли, а также данные о состоянии журнала в ходе выполнения тестов. Когда появится надпись **Тесты завершены** , нажмите любую клавишу, чтобы закрыть окно.

3. Используйте **проводник** для поиска каталога, который содержит проект, например **C:\Users\<имя_пользователя>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. В этом каталоге откройте **Bin**, а затем щелкните **Отладка**. Отобразятся текстовые файлы, которые были созданы во время выполнения тестов: sentences.txt, counter.txt и splitter.txt. Откройте каждый текстовый файл и проверьте данные.

   > [!NOTE]
   > Строковые данные сохраняются в этих файлах как массив значений типа decimal. Например, \[[97,103,111]] в файле **splitter.txt** — это слово *and*.

> [!NOTE]
> Перед развертыванием в Storm в кластере HDInsight не забудьте вернуть для параметра **Тип проекта** значение **Библиотека классов**.

### <a name="log-information"></a>Информация о журнале

С помощью `Context.Logger`можно легко записывать информацию из компонентов топологии в журнал. Например, приведенная ниже команда создает информационную запись журнала:

```csharp
Context.Logger.Info("Component started");
```

Записанную информацию можно просмотреть в **журнале службы Hadoop**, который можно найти в **обозревателе сервера**. Разверните запись для Storm в кластере HDInsight, а затем — **Журнал службы Hadoop**. Выберите файла журнала для просмотра.

> [!NOTE]
> Журналы хранятся в используемой в кластере учетной записи хранения Azure. Для просмотра журналов в Visual Studio необходимо войти в подписку Azure, содержащую эту учетную запись хранения.

### <a name="view-error-information"></a>Просмотр информации об ошибке

Чтобы просмотреть ошибки, произошедшие в работающей топологии, сделайте следующее.

1. В **обозревателе сервера** щелкните правой кнопкой мыши Storm в кластере HDInsight и выберите **Просмотреть топологии Storm**.

2. Для элементов **spout** и **bolt** столбец **Последняя ошибка** содержит сведения о последней ошибке.

3. Выберите **идентификатор воронки** или **идентификатор сита** для компонента с ошибкой. На отобразившейся странице подробностей в разделе **Ошибки** в нижней части страницы указаны дополнительные сведения об ошибке.

4. Дополнительные сведения можно получить, выбрав **Порт** в разделе **Исполнители** на этой странице и просмотрев журнал рабочих процессов Storm за последние несколько минут.

### <a name="errors-submitting-topologies"></a>Ошибки при отправке топологии

Если при отправке топологии в HDInsight возникают ошибки, можно найти журналы для серверных компонентов, которые обрабатывают отправку топологии в кластер HDInsight. Чтобы получить эти журналы, выполните следующую команду в командной строке.

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Замените __sshuser__ именем учетной записи пользователя SSH для кластера. Замените __clustername__ именем кластера HDInsight. Дополнительные сведения об использовании `scp` и `ssh` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Отправка может завершиться ошибкой по нескольким причинам:

* Пакет JDK не установлен или не находится по требуемому пути.
* Необходимые зависимости Java не включены в отправляемые данные.
* Несовместимые зависимости.
* Повторяющиеся имена топологий.

Если журнал `hdinsight-scpwebapi.out` содержит `FileNotFoundException`, это может быть вызвано указанными ниже причинами.

* Пакет JDK не находится по требуемому пути в среде разработки. Убедитесь в том, что пакет JDK установлен в среде разработки, а `%JAVA_HOME%/bin` находится по требуемому пути.
* Отсутствует зависимость Java. Убедитесь в том, что все необходимые файлы JAR включены в отправляемые данные.

## <a name="next-steps"></a>Дополнительная информация

Пример обработки данных из концентраторов событий см. в статье [Обработка событий из концентраторов событий Azure с помощью Storm в HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Пример топологии C#, которая разбивает поток данных на несколько потоков, см. на странице с [примером C# Storm](https://github.com/Blackmist/csharp-storm-example).

Дополнительные сведения о создании топологий C# см. в [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Другие методы работы с HDInsight и дополнительные примеры Storm в HDInsight см. в следующих статьях.

**Microsoft SCP.NET**

* [Руководство по программированию для SCP](apache-storm-scp-programming-guide.md)

**Apache Storm в HDInsight**

* [Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight.](apache-storm-deploy-monitor-topology.md)
* [Примеры топологий для Storm в HDInsight](apache-storm-example-topology.md)

**Apache Hadoop в HDInsight**

* [Использование Hive с Hadoop в HDInsight](../hadoop/hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](../hadoop/hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase в HDInsight**

* [Начало работы с HBase в HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)

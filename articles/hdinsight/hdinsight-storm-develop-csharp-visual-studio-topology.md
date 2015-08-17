<properties
   pageTitle="Топологии Apache Storm с помощью Visual Studio и C# | Microsoft Azure"
   description="Информация о создании топологии Storm на C# путем создания простой топологии статистики в Visual Studio с помощью средств HDInsight для Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/21/2015"
   ms.author="larryfr"/>

# Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio

Информация о создании топологии Storm на C# с помощью средств HDInsight для Visual Studio. В этом учебнике приведены пошаговые инструкции по созданию нового проекта Storm в Visual Studio, его локальному тестированию и развертыванию в Apache Storm в кластере HDInsight.

Вы также узнаете о том, как создавать гибридные топологии, использующие компоненты C# и Java.

##Предварительные требования

-	Одна из следующих версий Visual Studio

	-	Visual Studio 2012 с [обновлением 4](http://www.microsoft.com/download/details.aspx?id=39305)

	-	Visual Studio 2013 с [обновлением 4](http://www.microsoft.com/download/details.aspx?id=44921) или [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

	-	Visual Studio 2015 или [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

-	Пакет SDK Azure 2.5.1 или более поздней версии

-	Средства HDInsight для Visual Studio. Инструкции по установке и настройке средств HDInsight для Visual Studio см. в статье [Начало работы со средствами HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    > [AZURE.NOTE]Средства HDInsight для Visual Studio не поддерживаются в Visual Studio Express

-	Кластер Apache Storm в HDInsight. Инструкции по созданию кластера см. в статье [Начало работы с Apache Storm в HDInsight](hdinsight-storm-getting-started.md).

	> [AZURE.NOTE]В настоящее время инструменты HDInsight для Visual Studio поддерживают только Storm в кластерах HDInsight версии 3.2.

##Шаблоны

Средства HDInsight для Visual Studio предоставляют следующие шаблоны.

| Тип проекта | Что демонстрирует |
| ------------ | ------------- |
| Приложение Storm | Пустой проект топологии Storm |
| Пример модуля записи Storm Azure SQL | Запись в базу данных SQL Azure |
| Пример модуля чтения Storm DocumentDB | Чтение из Azure DocumentDB |
| Пример модуля записи Storm DocumentDB | Запись в Azure DocumentDB |
| Пример модуля чтения концентратора событий Storm | Чтение из концентраторов событий Azure |
| Пример модуля записи концентратора событий Storm | Запись в концентраторы событий Azure |
| Пример модуля чтения Storm HBase | Чтение из HBase в кластерах HDInsight |
| Пример модуля записи Storm HBase | Запись в HBase в кластерах HDInsight |
| Пример Storm Hybrid | Использование компонентов Java |
| Пример Storm | Базовая топология подсчета слов |

> [AZURE.NOTE]Примеры модулей чтения и записи HBase используют API REST HBase для взаимодействия с HBase в кластере HDInsight, а не API Java HBase.

На данном этапе в этом документе используется базовый тип проекта приложения Storm для создания новой топологии.

##Создание топологии на C#

1.	Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью [Начало работы со средствами HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2.	Откройте Visual Studio, выберите **Файл** > **Создать**, а затем **Проект**.

3.	В диалоговом окне **Новый проект** разверните **Установленные** > **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна введите имя приложения **WordCount**.

	![изображение](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.	После создания проекта у вас должны быть следующие файлы:

	-	**Program.cs** — определяет топологию для проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одной воронки и одного сита.

	-	**Spout.cs** — пример воронки, выдающей случайные числа.

	-	**Bolt.cs** — пример сита, которое подсчитывает числа, созданные воронкой.

	В рамках создания проекта с NuGet загружаются актуальные версии [пакетов SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

В следующих разделах рассказывается о том, как превратить этот проект в базовое приложение WordCount.

###Реализация воронки

1.	Откройте файл **Spout.cs**. Воронки используются для считывания данных в топологии из внешнего источника. Ниже перечислены основные компоненты воронки.

	-	**NextTuple** (Следующий кортеж) — вызывается Storm, когда воронке разрешено создавать новые кортежи.

	-	**Ack** (Подтверждение, только для транзакционной топологии) — обрабатывает подтверждения, инициированные другими компонентами топологии для кортежей, отправленных из этой воронки. Подтверждение кортежа позволяет воронке определить, что он был успешно обработан нижестоящими компонентами.

	-	**Fail** (Сбой, только для транзакционной топологии) — обрабатывает кортежи, обработка которых другими компонентами топологии завершилась сбоем. Это дает возможность повторно создать кортеж для повторной обработки.

2.	Замените содержимое класса **Spout** следующим кодом. В результате будет создана воронка, которая случайным образом отправляет предложения в топологию.

	```
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

	Просмотрите комментарии, чтобы понять, что делает этот код.

###Реализация сит

1.	Удалите существующий файл **Bolt.cs** из проекта.

2.	Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Добавить** > **Новый элемент**. Выберите в списке **Сито воронки** и введите имя **Splitter.cs**. Повторите этот процесс для создания второго сита с именем **Counter.cs**.

	-	**Splitter.cs** — реализует сито, которое делит предложения на отдельные слова и выдает новый поток слов.

	-	**Counter.cs** — реализует сито, которое подсчитывает каждое слово, и выдает новый поток из слов и частоту их употребления.

	> [AZURE.NOTE]Хотя эти сита просто считывают данные и записывают их в потоки, их можно также использовать для взаимодействия с базой данных, службой, и т. д.

3.	Откройте файл **Splitter.cs**. Обратите внимание, что он содержит только один метод по умолчанию — **Execute**. Он вызывается, когда сито получает кортеж для обработки. Здесь можно считывать и обрабатывать входящие кортежи и создавать исходящие кортежи.

4.	Замените содержимое файла класса **Splitter** следующим кодом:

	```
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

	Просмотрите комментарии, чтобы понять, что делает этот код.

5.	Откройте файл **Counter.cs** и замените содержимое класса следующим кодом:

	```
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

	Просмотрите комментарии, чтобы понять, что делает этот код.

###Определение топологии

Потоки данных между компонентами могут быть представлены в виде диаграммы воронок и сит. Граф для этой топологии выглядит следующим образом.

![изображение расположение компонентов](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Предложения отправляются из воронок и распределяются между экземплярами сита Splitter. Сито Splitter разбивает предложения на слова, которые распределяются среди экземпляров сита Counter.

Так как статистика хранится локально в экземпляре Counter, нужно убедиться, что определенные слова поступают в один и тот же экземпляр сита Counter, то есть каждое конкретное слово отслеживается только одним экземпляром. Однако для сита Splitter на самом деле неважно какое предложение получает то или иное сито. Это необходимо лишь для того, чтобы сбалансировать распределение предложений среди экземпляров.

Откройте файл **Program.cs**. Здесь важным методом является **ITopologyBuilder**, который используется для определения топологии, которая отправляется в Storm. Для реализации топологии, описанной выше, замените содержимое файла **ITopologyBuilder** следующим кодом:

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

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
        {"topology.kryo.register","["[B"]"}
    });

    return topologyBuilder;
```

Просмотрите комментарии, чтобы понять, что делает этот код.

##Отправка топологии

1.	В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Отправить в Storm в HDInsight**.

	> [AZURE.NOTE]При появлении запроса введите учетные данные входа для своей подписки Azure. Если у вас несколько подписок, воспользуйтесь той, что содержит Storm в кластере HDInsight.

2.	Выберите Storm в кластере HDInsight из раскрывающегося списка **Кластер Storm** и выберите **Отправить**. Вы можете отслеживать выполнение отправки в окне **Выходные данные**.

3.	После успешной отправки топологии должны появиться **топологии Storm** для кластера. Выберите топологию **WordCount** в списке, чтобы просмотреть информацию о выполняемой топологии.

	> [AZURE.NOTE]Вы также можете просмотреть **топологии Storm** из **обозревателя сервера**. Для этого разверните **Azure** > **HDInsight**, щелкните правой кнопкой мыши Storm в кластере HDInsight и выберите пункт **Просмотреть топологии Storm**.

	Используйте ссылки на компоненты spout или bolt, чтобы просмотреть информацию о них. Для каждого выбранного элемента открывается новое окно.

4.	В представлении **Topology Summary** (Сводка по топологии) выберите **Kill** (Удалить), чтобы остановить топологию.

	> [AZURE.NOTE]Топологии Storm выполняются, пока не будут удалены или пока не будет удален кластер.

##Транзакционная топология

Предыдущая топология является нетранзакционной. Компоненты топологии не реализуют функциональные возможности для воспроизведения сообщений при сбое обработки компонентом топологии. Чтобы получить пример транзакционной топологии, создайте новый проект и выберите в качестве типа проекта **Storm Sample** (Пример Storm).

Транзакционные топологии реализуют следующие возможности, обеспечивающие воспроизведение данных:

-	**Metadata caching**(Кэширование метаданных) — в воронке должны храниться метаданные для отправляемых данных, чтобы их можно было извлечь и отправить повторно в случае сбоя. Так как в этом примере отправляются данные небольшого размера, необработанные данные каждого кортежа хранятся в словаре для воспроизведения.

-	**Ack**(Подтверждение) — каждое сито в топологии может вызвать `this.ctx.Ack(tuple)`, чтобы подтвердить успешную обработку кортежа. Как только все сита подтвердят обработку кортежа, для воронки вызывается метод `Ack`. Это позволяет воронке удалить кэшированные данные для воспроизведения, так как они полностью обработаны.

-	**Fail**(Сбой) — каждое сито может вызвать `this.ctx.Fail(tuple)`, указав тем самым на сбой при обработке кортежа. Информация о сбое передается в метод `Fail` воронки, в которой кортеж может быть воспроизведен с помощью кэшированных метаданных.

-	**Sequence ID**(Идентификатор последовательности) — при отправке кортежа может быть указан идентификатор последовательности. Это должно быть значение, по которому кортеж можно идентифицировать для обработки воспроизведения (подтверждения и сбоя). Например, воронка в проекте **Storm Sample** (Пример Storm) при отправке данных использует следующий код:

	```
	this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
	```

	При этом в поток по умолчанию отправляется новый кортеж, содержащий предложение. Значение идентификатора последовательности содержится в **lastSeqId**. В этом примере **lastSeqId** просто увеличивается после каждой отправки кортежа.

Как показано в проекте **Storm Sample** (Пример Storm), транзакционность компонента можно задать во время выполнения топологии в соответствии с конфигурацией.

##Гибридная топология

Средства HDInsight для Visual Studio можно также использовать для создания гибридных топологий, в которых одни компоненты написаны на C#, а другие — на Java.

Чтобы получить пример гибридной топологии, создайте новый проект и выберите **Storm Hybrid Sample** (Пример гибридного решения Storm). В результате будет создан полностью закомментированный пример, содержащий несколько топологий, которые демонстрируют следующее:

-	**Воронка на Java** и **сито на C#** — определены в **HybridTopology\_javaSpout\_csharpBolt**

	-	Транзакционная версия определена в **HybridTopologyTx\_javaSpout\_csharpBolt**

-	**C# spout** и **Java bolt** — определены в **HybridTopology\_csharpSpout\_javaBolt**

	-	Транзакционная версия определена в **HybridTopologyTx\_csharpSpout\_javaBolt**

		> [AZURE.NOTE]В этой версии также демонстрируется использование кода Clojure из текстового файла в качестве компонента Java.

Для переключения между топологией, используемой при отправке проекта, перед отправкой в кластер переместите оператор `[Active(true)]` в топологию, которую необходимо использовать.

> [AZURE.NOTE]Все необходимые файлы Java предоставляются в составе этого проекта и находятся в папке **JavaDependency**.

При создании и отправке гибридной топологии, используются следующие компоненты:

-	**JavaComponentConstructor** должен использоваться для создания нового экземпляра класса Java для воронки или сита.

-	**microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** должен использоваться для сериализации данных в компоненты Java и из них из объектов Java в JSON.

-	При отправке топологии на сервер необходимо использовать параметр **Additional configurations** (Дополнительные конфигурации), чтобы задать **пути к файлам Java**. При этом следует указать путь к каталогу, содержащему JAR-файлы с классами Java.

###Концентраторы событий Azure

В SCP.Net версии 0.9.4.203 появился новый класс и метод, предназначенные специально для работы с воронкой концентратора событий (воронкой Java, которая считывает из концентратора событий.) При создании топологии, которая использует эту воронку, используйте следующие методы:

-	Класс **EventHubSpoutConfig**: создает объект, который содержит настройки для компонента воронки.

-	Метод **TopologyBuilder.SetEventHubSpout**: добавляет компонент воронки концентратора событий к топологии.

> [AZURE.NOTE]Хоть они и упрощают работу с воронкой концентратора событий по сравнению с другими компонентами Java, необходимо по-прежнему использовать CustomizedInteropJSONSerializer для сериализации данных, созданных воронкой.

##Обновление SCP.NET

Последние версии SCP.NET поддерживают обновление пакета через NuGet. Если доступно новое обновление, вы получите уведомление об обновлении. Чтобы вручную проверить обновления, выполните следующие действия.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.

2. Из диспетчера пакетов выберите **Обновления**. Если обновление доступно, он будет отображено. Нажмите кнопку **Обновление** для его установки.

> [AZURE.IMPORTANT]Если проект был создан с помощью одной из предыдущих версий SCP.NET, в которой не использовалось средство NuGet для обновления пакета, выполните следующие действия для обновления до новой версии.
>
> 1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.
> 2. С помощью поля **поиска** осуществите поиск и затем добавьте **пакет SDK Microsoft.SCP.Net.** в проект.

##Устранение неполадок

###Локальная проверка топологии

Хотя топологию легко развернуть в кластер, иногда ее необходимо проверять локально. Чтобы запустить и протестировать пример топологии, описанной в этой статье, локально в среде разработки, сделайте следующее.

> [AZURE.WARNING]Локальное тестирование работает только для базовых топологий на C#. Не следует использовать локальное тестирование для гибридных топологий или топологий, использующих несколько потоков, поскольку будут возникать ошибки.

1.	В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Свойства**. В свойствах проекта измените **Тип выходных данных** на **Консольное приложение**.

	![тип выходных данных](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

	> [AZURE.NOTE]Перед развертыванием топологии в кластере не забудьте вернуть для параметра **Тип выходных данных** значение **Библиотеки классов**.

2.	В **обозревателе решений** щелкните правой кнопкой мыши проект, а затем выберите **Добавить** > **Новый элемент**. Щелкните **Класс** и введите имя класса **LocalTest.cs**. Теперь нажмите кнопку **Добавить**.

3.	Откройте класс **LocalTest.cs** и добавьте в начало следующий оператор **using**:

	```
	using Microsoft.SCP;
	```

4.	Вставьте в класс **LocalTest** следующий код:

	```
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

5.	Откройте **Program.cs** и добавьте следующий код в метод **Main**.

	```
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

6.	Сохраните изменения, а затем нажмите клавишу **F5** или щелкните **Отладка** > **Начать отладку**, чтобы запустить проект. Должно появится окно консоли, а также данные о состоянии журнала в ходе выполнения тестов. Когда появится надпись **Тесты завершены**, нажмите любую клавишу, чтобы закрыть окно.

7.	Используйте **проводник** для поиска каталога, который содержит проект, например **C:\\Users < имя\_пользователя > \\Documents\\Visual Studio 2013\\Projects\\WordCount\\WordCount**. В этом каталоге откройте **Bin**, а затем нажмите **Отладка**. Отобразятся текстовые файлы, которые были созданы во время выполнения тестов: sentences.txt, counter.txt и splitter.txt. Откройте каждый текстовый файл и проверьте данные.

	> [AZURE.NOTE]Строковые данные сохраняются в этих файлах как массив значений типа decimal. Например [[97,103,111]] в файле **splitter.txt** — это слово and (и).

Хотя процедура локального тестирования базового приложения подсчета статистики довольно проста, ее истинное значение раскрывается при наличии сложной топологии, которая взаимодействует с внешними источниками данных или выполняет сложный анализ данных. Возможно, при работе с таким проектом нужно будет установить точки останова и обеспечить пошаговое прохождение кода в компонентах для локализации проблем.

> [AZURE.NOTE]Перед развертыванием в Storm в кластере HDInsight не забудьте вернуть для параметра **Тип проекта** значение **Библиотека классов**.

###Информация о журнале

С помощью `Context.Logger` можно легко записывать информацию из компонентов топологии в журнал. Например, приведенный ниже код создаст информационную запись в журнале:

```
Context.Logger.Info("Component started");
```

Записанную информацию можно просмотреть в **журнале службы Hadoop**, который можно найти в **обозревателе сервера**. Разверните запись для Storm в кластере HDInsight, а затем — **Журнал службы Hadoop**. Выберите файла журнала для просмотра.

> [AZURE.NOTE]Журналы хранятся в используемой в кластере учетной записи хранения Azure. Если эта подписка отличается от той, которая использовалась для входа в Visual Studio, для просмотра этой информации необходимо войти в подписку, содержащую эту учетную запись хранения.

###Просмотр информации об ошибке

Чтобы просмотреть ошибки, произошедшие в работающей топологии, сделайте следующее.

1.	В **обозревателе сервера** щелкните правой кнопкой мыши Storm в кластере HDInsight и выберите **Просмотреть топологии Storm**.

2.	Для **воронок** и **сит** столбец **Последняя ошибка** будет содержать информацию о последней возникшей ошибке.

3.	Выберите **идентификатор воронки** или **идентификатор сита** для компонента с ошибкой. На отобразившейся странице подробностей в разделе **Ошибки** в нижней части страницы будет указана дополнительная информация об ошибке.

4.	Чтобы получить дополнительную информацию, выберите **Порт** в разделе **Исполнители** на этой странице, чтобы просмотреть журнал рабочих процессов Storm за последние несколько минут.

##Дальнейшие действия

Теперь, когда вы узнали, как разрабатывать и развертывать топологии Storm с помощью средств HDInsight для Visual Studio, вы можете изучить [обработку событий из концентратора событий Azure с помощью Storm в HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Пример топологии C#, которая разбивает поток данных на несколько потоков, см. на странице [пример C# Storm](https://github.com/Blackmist/csharp-storm-example).

Для получения дополнительных сведений о создании топологии C# перейдите по ссылке [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Другие методы работы с HDInsight и дополнительные примеры Storm в HDinsight см. в следующих статьях.

**Apache Storm в HDInsight**

-	[Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight.](hdinsight-storm-deploy-monitor-topology.md)

-	[Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop в HDInsight**

-	[Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

-	[Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

-	[Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase в HDInsight**

-	[Начало работы с HBase в HDInsight](../hdinsight-hbase-get-started.md)

<!---HONumber=August15_HO6-->
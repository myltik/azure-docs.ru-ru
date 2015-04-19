<properties
   pageTitle="Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio | Azure"
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio

Информация о том, как создать топологию Storm на C# с помощью средств HDInsight для Visual Studio. В этом документе приведены пошаговые инструкции по созданию нового проекта Storm в Visual Studio, его локальному тестированию и развертыванию в Apache Storm в кластере HDInsight.

Вы также узнаете о том, как создать гибридные топологии, использующие компоненты C# и Java.

##Предварительные требования

* Одна из следующих версий Visual Studio

	* Visual Studio 2012 с <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">обновлением 4</a>

	* Visual Studio 2013 с <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">обновлением 4</a> или <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

* Пакет SDK для Azure 2.5.1 или более поздней версии

* Средства HDInsight для Visual Studio - инструкции по установке и настройке средств HDInsight для Visual Studio см. в статье <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Приступая к работе со средствами HDInsight для Visual Studio</a>.

* Кластер Apache Storm в HDInsight - инструкции по созданию кластера см. в статье <a href="../hdinsight-storm-getting-started/" target="_blank">Приступая к работе с Apache Storm в HDInsight</a>

	> [AZURE.NOTE] В настоящее время инструменты HDInsight для Visual Studio поддерживают только Storm на кластере HDInsight версии 3.2.

##Создание топологии на C#

1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Приступая к работе со средствами HDInsight для Visual Studio</a>.

2. Откройте Visual Studio, выберите **Файл**, **Создать**, а затем - **Проект**.

3. В диалоговом окне **Новый проект** разверните **Установленные**, **Шаблоны** и выберите **HDInsight**. В списке шаблонов выберите **Приложение Storm**. В нижней части диалогового окна, введите имя приложения **WordCount**.

	![image](./media/hdinsight-storm-develop-csharp-visual-studio/new-project.png)

4. После создания проекта у вас должны быть следующие файлы:

	* **Program.cs** - определяет топологию для проекта. Обратите внимание, что по умолчанию создается топология, состоящая из одной воронки и одного сита.

	* **Spout.cs** - пример воронки, выдающей случайные числа.

	* **Bolt.cs** - пример сита, которое посчитывает числа, созданные воронкой.

	В рамках создания проекта с Nuget скачиваются актуальные версии  <a href="https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/" target="_blank">пакетов SCP.NET</a>.

В следующих разделах рассказывается о том, как превратить этот проект в базовое приложение WordCount.

###Реализация воронки

5. Откройте **Spout.cs**. Воронки используются для считывания данных в топологии из внешнего источника. Ниже перечислены основные компоненты воронки.

	* **NextTuple** - вызывается Storm, когда воронке разрешено создавать новые кортежи.

	* **Ack** (только для транзакционной топологии) - обрабатывает подтверждения, инициированные другими компонентами топологии для кортежей, отправленных из этой воронки. Подтверждение кортежа позволяет воронке определить, что он был успешно обработан нижестоящими компонентами.

	* **Fail** (только для транзакционной топологии) - обрабатывает кортежи, при обработке которых другими компонентами топологии произошел сбой. Это дает возможность повторно создать кортеж для повторной обработки.

6. Замените содержимое класса Spout следующим кодом. В результате будет создана воронка, которая случайным образом отправляет предложения в топологию.

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

	Просмотрите комментарии, чтобы понять, что делает этот код.

###Реализация сит

1. Удалите существующий файл **Bolt.cs** из проекта.

2. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Добавить**, **Новый элемент**. Выберите в списке **Сито воронки** и введите имя **Splitter.cs**. Повторите этот процесс для создания второго сита с именем **Counter.cs**.

	* **Splitter.cs** - будет реализовывать сито, которое делит предложение на отдельные слова и выдает новый поток слов.

	* **Counter.cs** - будет реализовывать сито, которое считает, сколько раз встречается каждое слово, и выдает новый поток из слов и частоты их встречаемости.

	> [AZURE.NOTE] Хотя эти сита просто выполняют считывание и запись в потоки, их можно также использовать для взаимодействия с базой данных, службой, и т. д.

6. Откройте файл **Splitter.cs**. Обратите внимание, что он содержит только один метод по умолчанию - **Execute**. Он вызывается, когда сито получает кортеж для обработки. В ней можно считывать и обрабатывать входящие кортежи и создавать исходящие кортежи.

4. Замените содержимое файла класса **Splitter** следующим.

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

            // Get the sentance from the tuple
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

	Просмотрите код, чтобы понять, что он делает.

6. Откройте файл **Counter.cs** и замените содержимое класса следующим.

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

	Просмотрите код, чтобы понять, что он делает.

###Определение топологии

Потоки данных между компонентами могут быть представлены в виде диаграммы воронок и сит. Диаграмма для этой топологии выглядит следующим образом.

![image of how components are arranged](./media/hdinsight-storm-develop-csharp-visual-studio/wordcount-topology.png)

Предложения отправляются из воронок и распределяются между экземплярами сита Splitter. Сито Splitter разбивает предложения на слова, которые распределяются среди экземпляров сита Counter.

Так как статистика хранится локально в экземпляре Counter, нужно убедиться, что определенные слова поступают в один и тот же экземпляр сита Counter, то есть каждое конкретное слово отслеживается только одним экземпляром. Однако для сита Splitter на самом деле неважно какое предложение получает то или иное сито. Это необходимо лишь для того, чтобы сбалансировать распределение предложений среди экземпляров.

Откройте файл **Program.cs**. Здесь важным методом является **ITopologyBuilder**, который используется для определения топологии, которая отправляется в Storm.  Для реализации топологии, описанной выше, замените содержимое файла **ITopologyBuilder** следующим.

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
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Просмотрите код, чтобы понять, что он делает.

##Отправка топологии

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Отправить в Storm в HDInsight**.

	> [AZURE.NOTE] При появлении запроса введите учетные данные входа для своей подписки Azure. Если у вас несколько подписок, воспользуйтесь той, что содержит ваш кластер Storm в HDInsight.

2. Выберите кластер Storm в HDInsight из раскрывающегося списка **Кластер Storm** и выберите **Отправить**. Вы можете отслеживать, выполняется ли отправка успешно или нет, с помощью окна **Выходные данные**.

3. После успешной отправки топологии должны появиться **топологии Storm** для кластера. Выберите топологию **WordCount** в списке, чтобы просмотреть информацию о выполняемой топологии.

	> [AZURE.NOTE] Можно также просмотреть **топологии Storm** из **обозревателя серверов**, развернув **Azure**, **HDInsight** и затем щелкнув правой кнопкой мыши кластер Storm в HDInsight и выбрав пункт **Просмотреть топологии Storm**.

	Используйте ссылки на компоненты spout или bolt, чтобы просмотреть информацию о них. Для каждого выбранного элемента открывается новое окно.

4. В представлении **Сводка по топологиям** выберите **Удалить**, чтобы остановить топологию.

	> [AZURE.NOTE] Топологии Storm выполняются, пока не будут удалены или пока не будет удален кластер.

##Транзакционная топология

Предыдущая топология нетранзакционная - компоненты топологии не реализуют функциональные возможности для воспроизведения сообщений при сбое обработки компонентом топологии. Чтобы получить пример транзакционной топологии, создайте новый проект и выберите в качестве типа проекта **Пример Storm**.

Транзакционные топологии реализуют следующие возможности, обеспечивающие воспроизведение данных:

* **Кэширование метаданных** - в воронке должны храниться метаданные для отправляемых данных, чтобы данные можно было извлечь и отправить повторно в случае сбоя. Так как отправляемые данные в этом примере невелики, необработанные данные каждого кортежа хранятся в словаре для воспроизведения.

* **Подтверждение** - каждое сито в топологии может вызвать `this.ctx.Ack(tuple)` для подтверждения того, что оно успешно обработало кортеж. Как только все сита подтвердят обработку кортежа, для воронки вызывается метод `Подтверждение`. Это позволяет воронке удалить кэшированные для воспроизведения данные, так как они полностью обработаны.

* **Сбой** - каждое сито может вызвать `this.ctx.Fail(tuple)`, сообщив тем самым, что при обработке кортежа произошел сбой. Информация о сбое передается в метод `Сбой` воронки, в которой кортеж может быть воспроизведен с помощью кэшированных метаданных.

* **Идентификатор последовательности** - при отправке кортежа может быть указан идентификатор последовательности. Это должно быть значение, по которому кортеж можно идентифицировать для обработки воспроизведения (подтверждения и сбоя). Например, воронка в проекте **Пример Storm** при отправке данных использует следующий код:

		this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

	При этом в поток по умолчанию отправляется новый кортеж, содержащий предложение. Значение идентификатора последовательности содержится в lastSeqId. В этом примере lastSeqId просто увеличивается после каждой отправки кортежа.

Как показано в проекте **Пример Storm**, транзакционность компонента можно задать во время выполнения в соответствии с конфигурацией.

##Гибридная топология

Средства HDInsight для Visual Studio можно также использовать для создания гибридных топологий, в которых одни компоненты написаны на C#, а другие - на Java.

Чтобы получить пример гибридной топологии, создайте новый проект и выберите **Пример гибридного решения Storm**. В результате будет создан полностью закомментированный пример, содержащий несколько топологий, которые демонстрируют следующее:

* **Воронка на Java** и **сито на C#** - определены в **HybridTopology_javaSpout_csharpBolt**.

	* Транзакционная версия определяется в **HybridTopologyTx_javaSpout_csharpBolt**.

* **Воронка на C#** и **сито на Java** - определены в **HybridTopology_csharpSpout_javaBolt**.

	* Транзакционная версия определяется в **HybridTopologyTx_csharpSpout_javaBolt**

		> [AZURE.NOTE] В этой версии также демонстрируется использование кода clojure из текстового файла в качестве компонента Java.

Для переключения между топологией, используемой при отправке проекта, перед отправкой в кластер переместите оператор `[Active(true)]` в топологию, которую необходимо использовать.

> [AZURE.NOTE] Все необходимые файлы Java предоставляются в составе этого проекта и находятся в папке **JavaDependency**.

При создании и отправке гибридной топологии, используется следующее:

* **JavaComponentConstructor** должен использоваться для создания нового экземпляра класса Java для воронки или сита.

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** следует использовать для сериализации данных в компоненты Java и из них из объектов Java в JSON

* **При отправке** топологии на сервер необходимо использовать параметр **Дополнительные конфигурации**, чтобы задать **пути к файлам Java**. При этом следует указать путь к каталогу, содержащему JAR-файлы с классами Java.

##Устранение неполадок

###Локальная проверка топологии

Хотя топологию легко развернуть в кластер, иногда ее необходимо проверять локально. Чтобы запустить и протестировать пример топологии, описанной в этой статье, локально в среде разработки, сделайте следующее.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Свойства**. В свойствах проекта измените **Тип выходных данных** на **Консольное приложение**.

	![output type](./media/hdinsight-storm-develop-csharp-visual-studio/outputtype.png)

	> [AZURE.NOTE] Перед развертыванием топологии в кластере не забудьте вернуть для параметра **Тип выходных данных** значение **Библиотеки классов**.

2. В **обозревателе решений** щелкните правой кнопкой мыши проект, а затем выберите **Добавить**, **Новый элемент**. Щелкните **Класс** и введите имя класса **LocalTest.cs**. Наконец, щелкните **Добавить**.

3. Откройте **LocalTest.cs** и добавьте сверху следующую инструкцию `using`.

		using Microsoft.SCP;

3. Вставьте в класс **LocalTest** следующее содержимое.

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

	Ознакомьтесь с комментариями в коде. В этом коде для запуска компонентов в среде разработки используется **LocalContext**. При этом поток данных сохраняется между компонентами текстовых файлов на локальном диске.

2. Откройте **Program.cs** и добавьте следующий код в метод **Main**.

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

3. Сохраните изменения, а затем нажмите клавишу **F5** или щелкните **Отладки**, **Начать отладку** для запуска проекта. Должно появится окно консоли, а также данные о состоянии журнала в ходе выполнения тестов. Когда появится надпись **Тесты завершены**, нажмите любую клавишу, чтобы закрыть окно.

4. С помощью **обозревателя** перейдите в каталог, содержащий проект. Например, **C:\Users\<ваше_имя_пользователя>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. В этом каталоге откройте **Bin**, а затем - **Отладка**. Вы увидите текстовые файлы, созданные при выполнении тестов (sentences.txt, counter.txt и splitter.txt). Откройте каждый текстовый файл и проверьте данные.

	> [AZURE.NOTE] Строковые данные сохраняются в этих файлах как массив значений типа decimal. Например, [[97,103,111]] в файле **splitter.txt** - это слово 'and'.

Хотя процедура локального тестирования базового приложения подсчета статистики довольно проста, ее истинная ценность раскрывается при наличии сложной топологии, которая взаимодействует с внешними источниками данных или выполняет сложный анализ данных. Возможно, при работе с таким проектом нужно будет установить точки останова и обеспечить пошаговое прохождение кода в компонентах для локализации проблем.

> [AZURE.NOTE] Перед развертыванием в Storm в кластере HDInsight не забудьте вернуть для параметра **Тип проекта** значение **Библиотека классов**.

###Информация о журнале

С помощью `Context.Logger` можно легко записывать информацию из компонентов топологии в журнал. Например, приведенный ниже код создаст информационную запись в журнале.

	Context.Logger.Info("Component started");

Записанную информацию можно просмотреть в **журнале службы Hadoop**, который можно найти в **обозревателя сервера**. Разверните запись для Storm на кластере HDInsight, а затем - **Журнал службы Hadoop**. Выберите файла журнала для просмотра.

> [AZURE.NOTE] Журналы хранятся в используемой в кластере учетной записи хранения Azure. Если эта подписка отличается от той, которая использовалась для входа в Visual Studio, для просмотра этой информации необходимо войти в подписку, содержащую эту учетную запись хранения.

###Просмотр информации об ошибке

Чтобы просмотреть ошибки, произошедшие в работающей топологии, сделайте следующее.

1. В **обозревателе сервера** щелкните правой кнопкой мыши Storm в кластере HDInsight и выберите **Просмотреть топологии Storm**.

2. Для **воронок** и **сит** столбец **Последняя ошибка** будет содержать информацию о последней возникшей ошибке.

2. Выберите **идентификатор воронки** или **идентификатор сита** для компонента с ошибкой. На отобразившейся странице подробностей в разделе **Ошибки** в нижней части страницы будет указана дополнительная информация об ошибке.

3. Чтобы получить дополнительную информацию, выберите **Порт** в разделе **Исполнители** на этой странице, чтобы просмотреть журнал рабочих процессов Storm за последние несколько минут.


##Дальнейшие действия

Теперь, когда вы узнали, как разрабатывать и развертывать топологии Storm с помощью средств HDInsight для Visual Studio, вы можете изучить [обработку событий из концентратора событий Azure с помощью Storm в HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Для получения дополнительных сведений о создании топологии C# посетите [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Другие способы работы с HDInsight и дополнительные примеры Storm в HDinsight см. в следующих статьях.

**Apache Storm в HDInsight.**

* [Развертывание и мониторинг топологии с помощью Apache Storm в HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Обработка событий из концентратора событий Azure с помощью Storm в HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Разработка топологий на платформе Java для Apache Storm в HDInsight](hdinsight-storm-develop-java-topology.md)

* [Определение тенденций в Twitter по хэш-тегам с помощью Apache Storm в HDInsight](hdinsight-storm-twitter-trending.md)

* [Примеры Storm в HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)

**Apache Hadoop в HDInsight**

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase в HDInsight**

* [Приступая к работе с HBase в HDInsight](hdinsight-hbase-get-started.md)

<!--HONumber=47-->

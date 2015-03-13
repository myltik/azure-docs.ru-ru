<properties 
	pageTitle="Разработка приложений для обработки потоковых данных на SCP.NET с помощью Storm | Azure" 
	description="Вы узнаете, как разработать приложение для обработки потоковых данных на SCP.NET и C# с помощью Storm в HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#Разработка приложений для обработки потоковых данных на языке C# с помощью платформы SCP и Storm в HDInsight

Stream Computing Platform (SCP) - это платформа для создания надежных, распределенных, согласованных, высокопроизводительных и работающих в режиме реального времени приложений для обработки данных с использованием .NET. Она создана на базе [Apache Storm](http://storm.incubator.apache.org/) - системы с открытым исходным кодом, используемой для обработки потоковой передачи данных в режиме реального времени, которая доступна в HDInsight.

В этой статье вы узнаете следующее:

* Что такое платформа SCP и как она работает с Storm

* Как создать решение SCP

* Как протестировать решение SCP

* Как развернуть решение SCP в кластере Storm в HDInsight

##Предварительные требования

* Подписка Azure

* Кластер Storm в HDInsight

* Visual Studio 2010 или 2013

##Оглавление

* [SCP и Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP и Storm

Apache Storm - это распределенная система выполнения расчетов, которая работает в кластерах Hadoop и позволяет выполнять обработку данных в режиме реального времени. Поскольку система Storm работает на виртуальной машине Java (JVM), она была разработана таким образом, чтобы решения (называемые **топологиями**) могли быть реализованы на различных языках программирования. Вы даже можете создавать топологии, которые сочетают в себе компоненты, написанные на разных языках.

SCP предоставляет библиотеки, которые дают возможность с легкостью создавать решения Storm, используя .NET. Кластеры Storm в HDInsight включают в себя необходимые серверные компоненты для выполнения созданных вами решений SCP.

Дополнительные сведения о Storm в HDInsight см. в разделе [Обзор HDInsight Storm](http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/).

###Разработка решения SCP

SCP предоставляет интерфейсы, которые позволяют вам создавать следующие компоненты Storm:

* "Воронка" (spout) - использует данные из источника (файла, базы данных, API и т. д.) и формирует один или несколько потоков кортежей (упорядоченный список элементов)
* "Сито" (bolt) - использует один или несколько потоков и дополнительно формирует один или несколько потоков
* Топология - определяет поток данных между компонентами "воронка" и "сито", параллелизм этих компонентов и информацию о конфигурации

> [AZURE.NOTE] Компоненты "воронка" и "сито" также реализуют общую обработку. Например, "воронка" может разбивать входящее сообщение на несколько кортежей или просто формировать один кортеж и позволять "ситу" извлекать необходимые ему значения. Аналогичным образом, если вам необходимо записать данные в хранилище данных, вы это реализуете с помощью "сита".

Разработка решения включает в себя следующее:

* Какой источник данных вы будете использовать? Вам необходима "воронка", которая это реализует.
* Какая обработка должна происходить? Необходимо реализовать это в "воронках" и/или "ситах".
* Если обработка разбита на несколько "сит", как происходит поток данных между ними? Это нужно описать с помощью топологии.
* Как будет распределяться обработка между узлами в кластере HDInsight? Это нужно описать с помощью топологии.

##Установка пакета SDK для SCP

Пакет SDK для SCP поставляется с кластером Storm в HDInsight. После [создания кластера Storm в HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-storm-getting-started/) выполните следующие действия, чтобы скачать пакет SDK в локальную среду разработки.

1. Войдите на [портал управления Azure](https://manage.windowsazure.com).

2. На левой панели щелкните **HDINSIGHT**. Появится список развернутых кластеров HDInsight.

3. Щелкните кластер HDInsight, к которому хотите подключиться.

4. В верхней части страницы щелкните **КОНФИГУРАЦИЯ**.

5. В нижней части страницы щелкните **ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП**.

6. В мастере настройки удаленного рабочего стола введите **имя пользователя** и **пароль** для удаленного рабочего стола. Введите дату окончания срока действия в поле **ИСТЕКАЕТ**, а затем щелкните значок с галочкой.

7. После включения удаленного рабочего стола щелкните **ПОДКЛЮЧИТЬСЯ** в нижней части страницы и выполните дальнейшие инструкции.

8. После подключения к кластеру с помощью удаленного рабочего стола откройте **проводник** и в адресной строке введите **%storm_home%\examples**.

9. Щелкните правой кнопкой мыши папку **SDK** и выберите пункт **Копировать**.

10. В своей локальной среде разработки откройте **проводник** и перейдите в расположение, где хотите сохранить пакет SDK. Щелкните правовой кнопкой мыши и выберите пункт **Вставить**.

##Создание решения SCP

1. В Visual Studio создайте новый проект для **консольного приложения**. Назовите это решение **WordCount**.

2. В **обозревателе решений** щелкните правой кнопкой мыши папку **Ссылки** и выберите пункт **Добавить ссылку**.

3. Нажмите кнопку **Обзор** в нижней части **диспетчера ссылок**, а затем перейдите к папке, в которую вы ранее скачали пакет SDK. Выберите **Microsoft.SCPNet.dll** и **Microsoft.SCPLogger.dll**, а затем нажмите кнопку **Добавить**. Чтобы закрыть окно диспетчера ссылок, нажмите кнопку **ОК**.

###Создание "воронки"

1. В **обозревателе решений** щелкните правой кнопкой мыши **WordCount**, а затем выберите пункт **Добавить | Новый элемент**. Выберите **Класс** и введите имя **WordSpout.cs**. Наконец, нажмите кнопку **Добавить**.

2. Откройте файл **WordSpout.cs** и замените существующий код следующим кодом. Обязательно прочитайте комментарии, чтобы понимать, как этот код работает.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Создание тестов

1. В **обозревателе решений** щелкните правой кнопкой мыши **WordCount**, а затем выберите пункт **Добавить | Новый элемент**. Выберите **Класс** и введите имя **LocalTest.cs**. Наконец, нажмите кнопку **Добавить**.

2. Откройте файл **LocalTest.cs** и замените существующий код следующим кодом. Код, используемый для тестирования "воронки" очень похож на код, который будет использоваться для тестирования "сит".

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Откройте файл **Program.cs** и замените существующий код следующим кодом.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Запустите приложение. Когда оно закончит работу, посмотрите в каталог **WordCount\bin\debug** в своем проекте Visual Studio. Там должен быть файл с именем **spout.txt**, который содержит данные, сформированные во время этого теста "воронкой". Его содержимое должно выглядеть подобно этому тексту:

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] В вышеприведенных строках элемент Data является строкой, которую сформировала "воронка" и которая сохранена как массив байтов. Например, `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` - это строка "an apple a day keeps the doctor away".

###Создание "сит"

1. В **обозревателе решений** щелкните правой кнопкой мыши **WordCount**, а затем выберите пункт **Добавить | Новый элемент**. Выберите **Класс** и введите имя **SplitterBolt.cs**. Наконец, нажмите кнопку **Добавить**. Повторите этот шаг, чтобы добавить класс с именем **CounterBolt.cs**.

2. Откройте файл **SplitterBolt.cs** и замените существующий код следующим кодом.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Откройте файл **CounterBolt.cs** и замените его содержимое следующим.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] "Сито" формирует поток, который используется для тестирования. В реальном решении после обработки вы будете сохранять данные в базе данных, очереди или другом постоянном хранилище.

3. Откройте файл **LocalTest.cs** и добавьте следующие тесты для SplitterBolt и CounterBolt после предыдущего блока тестов "воронки".

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Запустите решение. Когда оно завершит выполнение, в каталоге **WordCount\bin\debug** должны появиться следующие файлы.

    * **spout.txt** - данные, сформированные WordSpout
    * **splitter.txt** - данные, сформированные SplitterBolt
    * **counter.txt** - данные, сформированные CounterBolt

###Добавление кода для запуска в кластере

1. Откройте файл **Program.cs** и замените строку `//Code to run on HDInsight cluster will go here` приведенной ниже, а затем перестройте проект.

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Создайте новый файл с именем **WordCount.spec**, и в качестве его содержимого используйте следующий текст. Он определяет топологию - компоненты, сколько экземпляров создавать на узлах в кластере и как осуществлять поток данных между ними. Текст написан с использованием [языка спецификации топологии](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Подключитесь к своему кластеру Storm с помощью удаленного рабочего стола и скопируйте папку **bin\debug** для локального проекта WordCount в кластер HDInsight Storm. Например, скопируйте ее в папку **%storm_home%\examples** и переименуйте в **WordCount**.

3. Также скопируйте файл **WordCount.spec** на сервер HDInsight. Поместите его в каталог **%storm_home%\examples**.

4. В кластере Storm используйте значок **командной строки Storm** на рабочем столе, чтобы открыть командную строку, а затем выполните следующую команду, чтобы запустить топологию WordCount.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    В результате будет создана новая папка с именем **temp** и использованы спецификации и файлы для решения WordCount, чтобы создать файл **WordCount.jar**, который затем отправляется в Storm.

5. После завершения выполнения команды откройте ссылку **Storm UI** на рабочем столе. В списке должна отобразиться топология **WordCount**. Вы можете выбрать топологию из **Storm UI**, чтобы просмотреть статистику.

6. Чтобы остановить топологию, в **Storm UI** выберите **WordCount**, затем из списка **действий топологии** выберите **Удалить**.

##Сводка

В описанных выше шагах вы узнали, как создавать, тестировать и развертывать базовую топологию подсчета слов, используя платформу SCP. Дополнительные примеры см. в каталоге **%storm_home%\examples** в своем кластере HDInsight. Дополнительную информацию о SCP см. в следующих ресурсах:

##Справочник по SCP

###Интерфейс дополнительного модуля SCP

Приложения SCP (известные как подключаемые модули) подключаются к конвейеру Storm. Подключаемый модуль - это консольное приложение .NET, которое реализует один или несколько следующих интерфейсов.  

-	**ISCPSpout** - используйте для нетранзакционных "воронок"
- 	**ISCPTxSpout** - используйте для транзакционных "воронок"
-	**ISCPBolt**  - используйте для нетранзакционных "сит"
-	**ISCPBatchBolt** - используйте для транзакционных "сит"

####ISCPPlugin

ISCPPlugin - это базовый интерфейс, от которого наследуют все остальные интерфейсы SCP. В данном случае мы работаем с имитатором интерфейса.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout - это интерфейс для нетранзакционных "воронок".  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Когда вы вызываете `NextTuple()`, код может отправить один или несколько кортежей. Если данных для отправки не существует, то возвращение метода должно произойти без отправки данных.

`Ack()` и `Fail()` будут вызываться, только если в файле спецификации включено подтверждение. Идентификатор `seqId` используется для определения подтверждения или отклонения принятия кортежа. Если подтверждение включено в нетранзакционной топологии, в "воронке" должны использоваться следующие функции отправки:  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Если подтверждение не поддерживается нетранзакционной топологией, функции `Ack()` и `Fail()` можно не заполнять кодом.  

Параметр `parms` для этих функций является пустым объектом Dictionary и зарезервирован для использования в дальнейшем.  

> [AZURE.NOTE] `NextTuple()`, `Ack()`, и `Fail()` вызываются в сплошном цикле в рамках одного потока. Если больше нет кортежей для передачи, используйте ключевое слово `sleep` с небольшим значением (например, 10 миллисекунд) для экономии ресурсов ЦП.

####ISCPTxSpout

ISCPTxSpout - это интерфейс для транзакционных "воронок".  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` вызывается, чтобы начать новую транзакцию. Идентификатор `seqId` используется для определения транзакции, что также используется в `Ack()` и `Fail()`. Данные, сформированные `NextTx()`, будут сохраняться в ZooKeeper, чтобы сделать возможной повторную передачу. Поскольку возможности хранилища ZooKeeper ограничены, вам следует передавать только метаданные, а не данные большого объема в транзакционных "воронках".  

Storm автоматически повторит транзакцию, если она завершилась с ошибкой в первый раз, поэтому в нормальных условиях команду Fail() вызывать не следует. Но так как платформа SCP может проверить метаданные, переданные транзакционной "воронкой", то она может вызывать команду Fail(), если метаданные неверны.

Параметр `parms` для этих функций является пустым объектом Dictionary и зарезервирован для использования в дальнейшем.

> [AZURE.NOTE] `NextTx()`, `Ack()`, и `Fail()` вызываются в сплошном цикле в рамках одного потока. Если больше нет кортежей для передачи, используйте ключевое слово `sleep` с небольшим значением (например, 10 миллисекунд) для экономии ресурсов ЦП.

####ISCPBolt

ISCPBolt - это интерфейс для нетранзакционных "сит".  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

При наличии нового кортежа для его обработки будет вызвана функция `Execute()`.

####ISCPBatchBolt

ISCPBatchBolt - это интерфейс для транзакционных "сит".  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

При наличии нового кортежа для его обработки будет вызвана функция `Execute()`. После завершения данной транзакции вызывается `FinishBatch()`.

Параметр `parms` для этих функций является пустым объектом Dictionary и зарезервирован для использования в дальнейшем.

> [AZURE.NOTE] "Сита", реализующие `ISCPBatchBolt`, могут получить `StormTxAttempt` из `parms`. `StormTxAttempt` можно использовать для того, чтобы определить, является ли кортеж исходным или попыткой повторной передачи. Этот процесс обычно происходит в подтверждающем "сите". См. пример с **HelloWorldTx**.  

###Объектная модель

SCP.NET предоставляет разработчикам простой набор ключевых объектов для программирования.

* `Context` - предоставляет приложению информацию о среде выполнения.

* `StateStore` - предоставляет службы метаданных, генерацию монотонных последовательностей и координацию без ожидания. Распределенные многозадачные абстракции высокого уровня могут создаваться на базе `StateStore`, включая создание распределенных блокировок, распределенных очередей данных, барьеров и служб транзакций.

* `SCPRuntime` - инициализирует среду выполнения и запускает подключаемые модули при выполнении решения в Storm.

* `LocalContext` - предоставляет методы для сериализации и десериализации сформированных кортежей в локальные файлы, когда приложение локально тестируется в Visual Studio.

####Context

Каждому экземпляру дополнительного модуля ISCP (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) соответствует экземпляр объекта Context. Функциональные возможности, предоставляемые Context, можно разделить на две части:

* **Статическая** - доступная во всем процессе C#.
* **Динамическая** - доступная для определенного экземпляра.  

**Статический контекст**

* `public static ILogger Logger = null;` - предоставляется для ведения журнала.  

* `public static SCPPluginType pluginType;` - возвращает тип подключаемого модуля для процесса C#. Если процесс C# запущен в локальном тестовом режиме (без Java), в качестве типа подключаемого модуля устанавливается SCP_NET_LOCAL.

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` - возвращает параметры конфигурации из виртуальной машины Java. Параметры передаются из виртуальной машины Java при инициализации подключаемого модуля. `Config` содержит два словаря:

    * `public Dictionary<string, Object> stormConf { get; set; }` - содержит параметры, определенные в Storm.

    * `public Dictionary<string, Object> pluginConf { get; set; }` - содержит параметры, определенные в SCP.

* `public static TopologyContext TopologyContext { get; set; } `  - возвращает контекст топологии. Он наиболее полезен для компонентов с множественным параллелизмом. В следующем примере показано, как получить доступ к контексту топологии.

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Динамический контекст**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` - объявляет схемы выходных и входных данных для потоков. Следующий пример объявляет схему ввода, которая состоит из одного кортежа строк, и схему вывода, которая состоит из кортежа строк и кортежа целых чисел.

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` - выдает один или несколько кортежей в поток по умолчанию. Следующий пример формирует два кортежа в поток по умолчанию.

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` - выдает один или несколько кортежей в заданный поток. Следующий пример формирует два кортежа в поток с именем 'mystream'.

        this.ctx.Emit("mystream", new Values(word, count));

При использовании нетранзакционных "воронок" и "сит", в которых включено подтверждение, воспользуйтесь следующим.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` - выдает один или несколько кортежей и идентификатор последовательности **из "воронки"** в заданный поток. Следующий пример формирует кортеж и идентификатор последовательности в поток по умолчанию.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` - выдает один или несколько кортежей и идентификатор последовательности **из "сита"** в заданный поток. Сформированные кортежи привязываются к входящим кортежам, указанным как `anchors`. Это позволяет выполнять подтверждения в обратном порядке вверх по конвейеру, следуя цепочке входящих/исходящих кортежей. Следующий пример передает кортеж и идентификатор последовательности в поток по умолчанию и привязывает порожденные кортежи к входящим кортежам, которые содержатся в `tuple`:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` - подтверждает кортеж. Вызывает функцию `ISCPSpout.Ack` "воронки", которая изначально произвела кортеж.

* `public abstract void Fail(SCPTuple tuple);` - отклоняет кортеж. Вызывает функцию `ISCPSpout.Fail` "воронки", которая изначально произвела кортеж.

####StateStore

Приложения SCP могут использовать объект `State` для сохранения информации в ZooKeeper, особенно в транзакционных топологиях. Это позволяет транзакционным "воронкам" получать состояние из ZooKeeper и перезапускать конвейер в случае сбоя.  

Объект `StateStore` предоставляет следующие методы.

* `public static StateStore Get(string storePath, string connStr);` - возвращает `StateStore` для заданного пути и строки подключения.

* `public State Create();` - создает новый объект `State` в этом экземпляре хранилища состояний.

* `public IEnumerable<State> GetUnCommitted();` - возвращает все объекты `State`, которые не являются зафиксированными, за исключением прерванных состояний.

* `public IEnumerable<State> States();` - возвращает все объекты 'State' в `StateStore`.


* `public T Get<T>(string info = null);` - возвращает объект `State` или `Registry`.

    * `info` - имя получаемого объекта `Registry`. Используется только при получении объекта `Registry`.

    * `T` - тип `State` или `Registry`.

* `public IEnumerable<Registry> Commited();` - возвращает объекты `Registry`, содержащие фиксированный `State`.

* `public IEnumerable<Registry> Aborted();` - возвращает объекты `Registry`, содержащие прерванный `State`.

* `public State GetState(long stateId)` - возвращает объект `State` для указанного идентификатора состояния.

Объект **State** предоставляет следующие методы.

* `public void Commit(bool simpleMode = true);` - задает состояние объекта `State` для фиксации.

    > [AZURE.NOTE] Если  `simpleMode` имеет значение "true", соответствующий Z-узел будет удален из ZooKeeper. В противном случае будет удален текущий Z-узел и создан новый в `COMMITTED_PATH`.

* `public void Abort();` - задает состояние объекта `State` для прерывания.

* `public void PutAttribute<T>(string key, T attribute);` - задает значение атрибута для заданного ключа.

    * `key` - ключ, для которого задан атрибут.

    * `attribute` - значение атрибута.

* `public T GetAttribute<T>(string key);` - возвращает значение атрибута для заданного ключа.

####SCPRuntime

SCPRuntime предоставляет следующие методы.

* `public static void Initialize();` - инициализирует среду выполнения SCP. При вызове процесс C# подключается к виртуальной машине Java (JVM) и получает параметры конфигурации и контекст топологии.

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` - запускает цикл обработки сообщений. В этом цикле подключаемый модуль C# будет получать сообщения от JVM (включая кортежи и контрольные сигналы), а затем обрабатывать сообщения.

    * `pluginDelegate` - делегат, который может возвращать объект, реализующий ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` - принимает кортежи из очереди.

* `void WriteMsgQueueToFile(string filepath, bool append = false);` - сохраняет кортежи в файл.

* `void ReadFromFileToMsgQueue(string filepath);` - считывает кортежи из файла.


###<a id="spec"></a>Язык спецификации топологии

Спецификация топологии SCP представляет собой специализированный язык для описания и настройки топологий SCP. Он основан на языке Storm [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html)  

Для определения топологии используйте следующие инструкции.

|Новые функции|	Параметры|	Описание
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Определение транзакционной топологии по имени, карта для определения "воронки" и "сита".
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Определение транзакционной "воронки". Будет запущено приложение с именем ***exec-name*** и аргументами ***args***.<br><br>***fields*** - это поля вывода для "воронки".
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Определение транзакционного пакетного "сита". Будет запущено приложение с именем ***exec-name*** и аргументами ***args***.<br><br>fields - это поля вывода для "сита".
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Определение транзакционного подтверждающего "сита" Будет запущено приложение с именем ***exec-name*** и аргументами args.<br><br>***fields*** - это поля вывода для "сита".
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Определение нетранзакционной топологии по имени,  карты для определения "воронок" и "сит".
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Определение нетранзакционной "воронки". Будет запущено приложение с именем ***exec-name*** и аргументами ***args***.<br><br>***fields*** - это поля вывода для "воронки".<br><br>***parameters*** не является обязательным и используется для указания некоторых параметров, например nontransactional.ack.enabled.
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Определение нетранзакционного "сита". Будет запущено приложение с именем ***exec-name*** и аргументами ***args***.<br><br>***fields*** - это поля вывода для "сита".<br><br>***parameters*** не является обязательным и используется для указания некоторых параметров, например nontransactional.ack.enabled.

Далее приводятся ключевые слова, которые можно использовать для определения топологии.

|Ключевые слова|	Описание
|---------|------------
|**:name**|	Определение имени топологии
|**:topology**|	Определение топологии с использованием функций, описанных выше, а также встроенных функций.
|**:p**|	Определение подсказок по параллелизму для каждой из "воронок" и "сита".
|**:config**|	Определение параметров конфигурации или обновление существующих параметров.
|**:schema**|	Определение Схемы потока данных

Часто используемые параметры

|Параметр|	Описание
|---------|------------
|**"plugin.name"**|	Имя exe-файла для дополнительного модуля на C#
|**"plugin.args"**|	Аргументы параметров дополнительного модуля
|**"output.schema"**|	Схема вывода
|**"nontransactional.ack.enabled"**|	Если подтверждение обработки для нетранзакционной топологии активно.

Спецификации топологии можно отправлять напрямую в кластер Storm, чтобы выполнять с помощью команды ***runspec***, которая находится в каталоге **%storm_home%\bin** в кластерах Storm.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] Параметр ***resource-dir*** не является обязательным. Вам необходимо указать его значение, если вы хотите запустить приложение C#. В этом каталоге будет находиться приложение, а также данные зависимостей и конфигураций.  

Параметр ***classpath*** также не является обязательным. Он используется для указания пути к классу Java, если файл со спецификацией содержит "воронку" или "сито" на Java.  

###Прочие характеристики

####Объявление схемы ввода и вывода

При вызове `Emit()` платформа сериализует кортеж в массив байтов и передает его в виртуальную машину Java. Затем Storm передаст этот кортеж целевым объектам. Затем "сита" получат кортеж. Для "сит" C# кортеж получается от JVM и преобразовывается обратно в исходный тип.

Для поддержки этих процессов сериализации и десериализации вам необходимо объявить схемы ввода и вывода. Они определяются как объекты `Dictionary<string, List<Type>`, где ключом является идентификатор потока, а значением - `Types` кортежей, которые будут сформированы. Компонент может объявлять несколько потоков.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

Объект `Context` предоставляет метод `DeclareComponentSchema`, который может использоваться в целях объявления схемы для сериализации или десериализации.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Вы должны убедиться, что сформированные кортежи данных подчиняются схеме, заданной для этого потока. В противном случае система создаст исключение во время выполнения.  

####Поддержка многопоточности.

Вы можете формировать несколько потоков, выполняя несколько вызовов `Emit()` и указывая параметр `streamId` для каждого потока, в который выполняется запись.

> [AZURE.NOTE] Передача в несуществующий поток приведет к созданию исключения во время выполнения.

####Объединение полей

Встроенная в Storm функция объединения полей не работает должным образом в SCP.NET. При проксировании данных в виртуальную машину Java все типы данных полей фактически являются `byte[]`, а объединение полей использует хэш-код объекта  `byte[]` для выполнения объединения. Хэш-код объекта `byte[]` является адресом этого объекта в памяти, поэтому объединение будет неправильным для двух объектов `byte[]`, если у них одинаковое содержимое, но не одинаковый адрес.

Для устранения этой проблемы используйте в своей спецификации `scp-field-group`. Таким образом, при выполнении объединения будет использоваться содержимое объекта `byte[]`. Далее приведен пример, как это использовать в спецификации.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

Этот пример выполняет следующие действия.

* `scp-field-group` - использование настраиваемого объединения.  
* `:tx` или `:non-tx` - указывает тип (транзакционный или нетранзакционный).
* `[0,1]` - использование набора хэширования идентификаторов полей, начиная с 0.  

####Гибридная топология

Большинство собственных "воронок", "сит" и топологий Storm реализовано в Java. Для поддержки повторного использования этих компонентов в решениях, которые используют компоненты C#, SCP дает вам возможность создавать и определять в спецификации гибридные топологии.

* **"Воронка" или "сито" на Java** - в файле спецификации также можно использовать `scp-spout` и `scp-bolt` для указания "воронок" и "сит" на Java. Далее показано, как указать "воронку" на Java с именем класса `microsoft.scp.example.HybridTopology.Generator`:

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Путь к классу Java** - при использовании "воронок" и "сит" на Java необходимо сначала скомпилировать "воронку" или "сито" в JAR-файл. Затем добавьте путь к классу Java с параметром`-cp` при использовании команды **runSpec**. Следующий пример включает в себя JAR-файлы, расположенные в каталоге **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Сериализация и десериализация между Java и C#** - чтобы сериализировать объекты между Java и C#, используйте `CustomizedInteropJSONSerializer`.

    > [AZURE.NOTE] В настоящее время `CustomizedInteropJSONSerializer` поддерживает только **"воронки" Java** и **"сита" C#**.

    * Укажите сериализатор для компонентов Java в файле спецификации

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Используйте сериализатор в своих компонентах C#

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    Стандартная сериализация должна работать должным образом в большинстве случаев, если тип данных не слишком сложный.  Если тип данных слишком сложный или если производительность стандартной сериализации не соответствует вашим требованиям, вы можете создать свою реализацию, которая удовлетворит ваши запросы. Для этого доступны следующие интерфейсы:

    **Интерфейс сериализации на языке Java**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Интерфейс десериализации на языке C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###Хост-режим SCP

Хост-режим позволяет вам скомпилировать свой проект в библиотеку DLL и разместить его с помощью **SCPHost.exe**. Мы советуем использовать этот способ размещения вашего решения в рабочей среде. При использовании хост-режима файл спецификации будет содержать `SCPHost.exe` в качестве подключаемого модуля.

Далее приведен пример файла спецификации, в котором использован хост-метод для примера HelloWorld.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - сборка, которая содержит "воронки" и "сита".
* **Scp.App.HelloWorld.Generator** - имя класса для "воронки", которая содержится в **HelloWorld.dll**.
* **Get** - метод, вызываемый для получения экземпляра "воронки".


##Примеры программирования SCP

Следующие примеры приложений, написанные с помощью SCP, вы можете найти в своем кластере HDInsight Storm в каталоге **%storm_home%\examples**.

* **HelloWorld** - это очень простой пример SCP.Net; он подобен примеру подсчета слов, использованному ранее в этой статье. Он использует нетранзакционную топологию, в которой "воронка" называется **generator**, а два "сита" - **splitter** и **counter**. "Воронка" **generator** случайным образом создает предложения и отправляет их в **splitter**. "Сито" **splitter** разбивает эти предложения на слова и отправляет их в "сито" **counter**. "Сито" **counter** использует словарь для записи частоты употребления каждого слова.

    Для этого примера есть два файла спецификации - **HelloWorld.spec** и **HelloWorld_EnableAck.spec**. При использовании **HelloWorld_EnableAck.spec** пример подтверждает кортежи, которые проходят через "сита", однако **splitter** разработан таким образом, чтобы некоторые "сита" случайным образом выдавали ошибку для демонстрации обработки ошибок в нетранзакционных топологиях.

* **HelloWorldTx** - пример реализации транзакционной топологии. Он содержит транзакционную "воронку" с именем **generator**, пакетное "сито" с именем **partial-count** и подтверждающее "сито" с именем **count-sum**. Также есть три предварительно созданных TXT-файла для использования с этой топологией: **DataSource0.txt**, **DataSource1.txt** и **DataSource2.txt**.

    При каждой транзакции "воронка" в случайном порядке выбирает два файла и отправляет два имени файла в "сито" **partial-count**. "Сито" получает имя файла от пришедшего кортежа данных, затем открывает файл и считывает количество слов в нем. И, наконец, оно отправляет данные с количеством слов в "сито" **count-sum**. "Сито" **count-sum** выводит конечный результат подсчета.  

    Для достижения семантики "только однажды" "сито" **count-sum** должно определить, не обрабатывает ли оно перезапущенную транзакцию. В этом примере транзакция имеет статическую переменную-член.  

        public static long lastCommittedTxId = -1;  

    Когда создается экземпляр "сита", он получает попытку транзакции (`txAttempt`) от входных параметров:  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    После того как вызвана `FinishBatch()`, параметр `lastCommittedTxId` обновляется, если транзакция не была перезапущена.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - данная топология содержит "воронку" на Java и "сито" на C#. В ней используются стандартные для платформы SCP процессы сериализации и десериализации. Более подробную информацию см. в **%storm_home%examples\HybridTopology\HybridTopology.spec**; в файле **SubmitTopology.bat** см., как указывать путь к классу Java при отправке топологии.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
<!--HONumber=42-->

<properties title="Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C# на Storm в HDInsight" pageTitle="Разработка приложений для обработки потоковых данных на SCP.NET с помощью Storm | Azure" description="Вы узнаете, как разработать приложение для обработки потоковых данных на SCP.NET и C# с помощью Storm в HDInsight." services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

# Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C\# на Storm в HDInsight

## Знакомство с SCP

SPC — это платформа для создания надежного, согласованного и высокопроизводительного приложения для обработки данных в реальном времени. Она построена поверх Storm (http://storm.incubator.apache.org/) — системы обработки потоков, разработанной сообществами OSS. Создателем Storm является Натан Марц (Nathan Marz), программа имеет открытый код и распространяется компанией Twitter. В ней используется ZooKeeper (http://zookeeper.apache.org/), другой проект Apache, который обеспечивает высоконадежную распределенную координацию и управление состояниями.

С помощью проекта SPC удалось не только перенести Storm в Windows, но и добавить расширения и адаптировать его для работы в экосистеме Windows. Дополнения включают в себя возможности разработки .NET и библиотеки; адаптация заключается в возможности развертывания на базе Windows.

Дополнения и адаптация выполнены таким образом, что нам не придется разветвлять проекты OSS, а можно использовать производные экосистемы, созданные на базе Storm.

## Модель обработки данных

Данные в SCP оформляются в виде постоянных потоков кортежей данных. Обычно кортежи оформляются в виде очереди на первом этапе, затем бизнес-логика приложения, находящегося внутри топологии Storm, подбирает и трансформирует их. Наконец, данные на выходе могут подаваться в виде кортежей в другую систему SCP или отправляться на хранение в системы распределенных файлов или базы данных, например SQL Server.

![][0]

В Storm вычислительные графы определяются топологией приложения. Каждый узел в топологии содержит логическую схему обработки информации, а по связям между узлами можно определить движение потока данных. Узлы, которые "вливают" входные данные в топологию, называются "воронками" (Spout) и могут использоваться для создания последовательности потока данных. Входящие данные могут располагаться в файловых журналах, экстракционной базе данных, системных счетчиках производительности и т. д. Узлы для входящих и исходящих потоков называются "ситами" (Bolt). В этих узлах происходит очистка, выборка и структурирование данных.

SCP поддерживает максимальное качество работы при типе обработки данных "минимум однажды" и "только однажды". В приложениях для обработки данных распределенных процессов во время обработки данных могут возникать ошибки. Например, отказ сети, аппаратный сбой, ошибка в коде пользователя и т. д. Модель обработки "минимум однажды" позволяет гарантировать минимум одну проверку путем повторной обработки данных при возникновении ошибки. Модель обработки "только однажды" проста и надежна и отлично подходит для многих приложений. Однако в случаях, когда приложение требует, например, точного подсчета данных, вид обработки "минимум однажды" недостаточен, поскольку одни и те же данные могут проходить через топологию приложения несколько раз. Для подобных случаев разработан вид обработки "только однажды". Он обеспечивает достоверность итоговых данных даже в тех случаях, когда данные могут посылаться повторно и обрабатываться несколько раз подряд.

SCP позволяет разработчикам .NET создавать приложения для обработки данных в реальном времени со скрытым использованием возможностей Storm на виртуальных Java-машинах (JVM). .NET и JVM обмениваются данными через локальный TCP-сокет. По сути, каждая пара "воронка/сито" — это пара процессов .Net/Java, в которой логика пользователя задействована в процессе .Net в качестве дополнительного модуля.

Для создания приложения для обработки данных поверх SCP необходимо выполнить следующее:

-   Создать и активировать "воронки" для извлечения данных из очереди.
-   Создать и активировать "сита" для обработки входящих данных и сохранения данных после обработки во внешнем хранилище, например в базе данных.
-   Создать топологию процесса, загрузить и запустить ее. Топология определяет вершины и поток данных между вершинами. SCP получает спецификацию топологии и размещает из в кластере Storm, где каждая вершина работает в отдельном логическом узле. Переход на резервный ресурс и масштабирование выполняет планировщик задач Storm.

В этой статье мы разберем несколько простых примеров создания приложения для обработки данных с помощью SCP.

## Интерфейс дополнительного модуля SCP

Дополнительные модули (или приложения) SCP — это автономные файлы типа EXE, которые можно запускать в Visual Studio во время фазы разработки, а также можно подключать к конвейеру Storm после завершения разработки. Процесс написания кода дополнительного модуля SCP ничем не отличается от процессов написания кода для стандартных приложений в консоли Windows. Платформа SCP.NET объявляет интерфейс для "воронки" и "сита", которые пользователю нужно заполнить программным кодом для модуля. Это делается для того, чтобы пользователь мог сосредоточиться на своей задаче и предоставил платформе SCP.NET позаботиться обо всех остальных аспектах.

Код дополнительного модуля нужно внести в один из следующих интерфейсов, в зависимости от того, является ли топология транзакционной или нетранзакционной и является ли компонент "воронкой" или "ситом".

-   ISCPSpout
-   ISCPBolt
-   ISCPTxSpout
-   ISCPBatchBolt

### ISCPPlugin

ISCPPlugin — это общий интерфейс для различных дополнительных модулей. В данном случае мы работаем с имитатором интерфейса.

    public interface ISCPPlugin
    {
    }  

### ISCPSpout

ISCPSpout — это интерфейс для нетранзакционных "воронок".

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Когда вы вызываете команду NextTuple(), код пользователя в С\# может отправить один или несколько кортежей данных. Если данных для отправки не существует, то возвращение метода должно произойти без отправки данных. Нужно отметить, что команды NextTuple(), Ack() и Fail() вызываются в сплошном цикле однопоточной обработки C\# процесса. Если кортежей для отправки не существует, то разумно будет приостановить NextTuple на короткий промежуток времени (около 10 миллисекунд), чтобы излишне не нагружать ЦП.

Команды Ack() и Fail() вызываются только в случае активации механизма подтверждения в файле спецификации. Уникальный идентификатор ("seqId") используется для определения подтверждения или отклонения принятия кортежа. Если подтверждение включено в нетранзакционной топологии, в "воронке" должны использоваться следующие функции отправки:

        public abstract void Emit(string streamId, List<object> values, long seqId);  

Если подтверждение не поддерживается нетранзакционной топологией, функции Ack() и Fail() можно не заполнять кодом.

Если входные параметры "parms" представляют собой пустой словарь, то их оставляют незаполненными для использования в будущем.

### ISCPBolt

ISCPBolt — это интерфейс для нетранзакционных "сит"

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Когда кортеж доступен для отправки, запускается команда Execute().

### ISCPTxSpout

ISCPTxSpout — это интерфейс для транзакционных "воронок".

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Так же, как и в случае с нетранзакционными "воронками", команды NextTx(), Ack() и Fail() вызываются в сплошном цикле однопоточной обработки C\# процесса. Если данных для отправки не существует, то разумно будет приостановить NextTuple на короткий промежуток времени (около 10 миллисекунд), чтобы излишне не нагружать ЦП.

Команда NextTx() вызывается для того, чтобы начать новую передачу, для обозначения потока транзакции используются идентификаторы выходного параметра "seqId", которые также используются в командах Ack() и Fail(). В случае с командой In NextTx() пользователь может отправлять данные на сторону Java. Данные сохраняются в ZooKeeper для возможности провести повторную передачу. Поскольку возможности хранения ZooKeeper ограничены, пользователи могут отправлять метаданные, но не могут отправлять данные большого объема в транзакционных "воронках".

Storm повторит передачу, если она завершилась с ошибкой в первый раз, поэтому в нормальных условиях команду Fail() вызывать не следует. Но поскольку SCP может проверить метаданные от транзакционной "воронки", то оно может вызывать команду Fail(), если метаданные неверны.

Если входные параметры "parms" представляют собой пустой словарь, то их оставляют незаполненными для использования в будущем.

### ISCPBatchBolt

ISCPBatchBolt — это интерфейс для транзакционных "сит".

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Команда Execute() вызывается в случае прибытия нового кортежа данных на сито. Команда FinishBatch() вызывается при завершении передачи. Входной параметр "parms" зарезервирован для использованию в будущем.

Для транзакционной топологии существует важная концепция — "StormTxAttempt". Она содержит два поля "TxId" и "AttemptId". "TxId" используется для определения специальных передач данных и для каждой отдельной передачи может предприниматься несколько попыток, если перед этим передача завершилась ошибкой и запущена повторно. SCP.NET создает отдельный объект ISCPBatchBolt для обработки "StormTxAttempt", точно так же, как это делает Storm на стороне Java. Это делается для поддержки параллельной обработки данных. Пользователю необходимо помнить, что после завершения попытки передачи соответствующий объект ISCPBatchBolt будет уничтожен и удален как мусор.

## Объектная модель

SCP.NET предоставляет разработчикам простой набор ключевых объектов для программирования. В их число входят: **Context**, **StateStore** и **SCPRuntime**. О них пойдет речь в оставшейся части раздела.

### Context

Context обеспечивает рабочую среду для приложений. Каждому экземпляру дополнительного модуля ISCP (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) соответствует экземпляр объекта Context. Функциональные возможности, предоставляемые Context, можно разделить на две части: (1) статическая часть, которая доступна во всем процессе C\#, (2) динамическая часть, которая доступна только для определенных экземпляров Context.

#### Статическая часть

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;
    public static Config Config { get; set; }
    public static TopologyContext TopologyContext { get; set; }  

"Logger" отвечает за создание записей в журнале.

"pluginType" используется для опознавания типа дополнительного модуля в процессе C\#. Если процесс C\# запущен в локальном тестовом режиме (без Java), в качестве типа дополнительного модуля устанавливается "SCP\_NET\_LOCAL".

    public enum SCPPluginType
    {
        SCP_NET_LOCAL = 0,
        SCP_NET_SPOUT = 1,
        SCP_NET_BOLT = 2,
        SCP_NET_TX_SPOUT = 3,
        SCP_NET_BATCH_BOLT = 4
    }  

"Config" предназначен для получения параметров конфигурации со стороны Java. Параметры передаются со стороны Java после запуска дополнительного модуля на C\#. Параметры "Config" делятся на две части: "stormConf" и "pluginConf"

    public Dictionary<string, Object> stormConf { get; set; }
    public Dictionary<string, Object> pluginConf { get; set; }  

"stormConf" — это параметры, установленные Storm, а "pluginConf" — это параметры установленные SCP. Например:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
    }  

"TopologyContext" необходим для получения контекста топологии и особенно полезен для компонентов с поддержкой многочисленных параллельных процессов обработки информации. Пример:

    //demo how to get TopologyContext info
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

#### Динамическая часть

Следующие интерфейсы привязаны к определенным экземплярам объекта Context. Экземпляр объекта Context создается платформой SCP.NET и передается в код пользователя.

    /* Declare the Output and Input Stream Schemas */
    public void DeclareComponentSchema(ComponentStreamSchema schema);
    /* Emit tuple to default stream. */
    public abstract void Emit(List<object> values);

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

Для нетранзакционных "воронок" с поддержкой функции подтверждения обработки данных предусмотрен следующий метод:

    /* for non-transactional Spout which supports ack */
    public abstract void Emit(string streamId, List<object> values, long seqId);

Для нетранзакционных "сит" с поддержкой функции подтверждения получения данных после принятия кортежа возможны только две команды: Ack() и Fail(). При отправке нового кортежа данных "сита" также должны указывать привязки для него. Существуют следующие методы.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);  

### StateStore

**StateStore** предоставляет службы метаданных, генерацию монотонных последовательностей и координацию без ожидания. Распределенные многозадачные абстракции высокого уровня могут создаваться на **StateStore**, включая создание распределенных блокировок, распределенных очередей данных, барьеров и служб транзакций.

Приложения SCP могут использовать объект **State** для отправки информации в ZooKeeper, особенно в транзакционных топологиях. Если во время этого процесса в транзакционной "воронке" происходит сбой и перезагрузка, вся необходимая информация может быть получена из ZooKeeper, и конвейер данных будет восстановлен.

Объекты **StateStore** в основном обладают следующими методами:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns>
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

Объекты **State** в основном обладают следующими методами:

    /// <summary>
    /// Set the status of the state object to commit
    /// </summary>
    public void Commit(bool simpleMode = true);

    /// <summary>
    /// Set the status of the state object to abort
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key
    /// </summary>
    /// <param name="key">Key</param>
    /// <param name="attribute">State Attribute</param>
    public void PutAttribute<T>(string key, T attribute);

    /// <summary>
    /// Get the attribute value associated with the given key
    /// </summary>
    /// <param name="key">Key</param>
    /// <returns>State Attribute</returns>
    public T GetAttribute<T>(string key);  

Для метода Commit(), когда значение simpleMode имеет значение "истинно", соответствующий Z-узел будет удален из ZooKeeper. В противном случае будет удален текущий Z-узел и создан новый в COMMITTED\_PATH.

### SCPRuntime

SCPRuntime обеспечивает работу двух методов:

        public static void Initialize();

        public static void LaunchPlugin(newSCPPlugin createDelegate);

Initialize() используется для запуска среды исполнения SCP. В этом методе процесс C\# соединяется со стороной Java и получает параметры конфигурации и контекст топологии.

LaunchPlugin() используется для запуска цикла обработки сообщений. В этом цикле дополнительный модуль C\# получает сообщения со стороны Java (включая кортежи данных и управляющие сигналы), а затем обрабатывает сообщения, с возможностью вызова метода интерфейса, описанного в коде пользователя. Входной параметр для метода LaunchPlugin() является делегатом, который может возвращать объект, реализующий интерфейс ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);  

Для ISCPBatchBolt мы можем получить "StormTxAttempt" из "parms" и определить, отправлялись ли эти данные повторно. Этот процесс обычно происходит в подтверждающем "сите". См. пример с "HelloWorldTx".

Как правило, дополнительный модуль SCP в такой ситуации может работать в двух режимах:

1.  Режим локальной проверки. В этом режиме дополнительные модули SCP (с кодом пользователя на C\#) активны в Visual Studio во время фазы разработки. В этом режиме может быть использован "LocalContext", который обеспечивает метод для сериализации отправленных кортежей в локальные файлы и выполняет обратное считывание в память.

        public interface ILocalContext
        {
            List<SCPTuple> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);
            void ReadFromFileToMsgQueue(string filepath);
        }  

2.  Обычный режим: В этом режиме дополнительные модули SCP запускаются процессом Storm Java.

    Ниже дан пример запуска дополнительного модуля SCP:

        namespace Scp.App.HelloWorld
        {
          public class Generator : ISCPSpout
          {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
              return new Generator(ctx);
            }
          }

          class HelloWorld
          {
            static void Main(string[] args)
            {
              /* Setting the environment variable here can change the log file name */
              System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

              SCPRuntime.Initialize();
              SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
        }
          }
        }

## Язык спецификации топологии

Спецификация топологии SCP представляет собой специализированный язык для описания и настройки топологий SCP. Он основан на Clojure DSL для Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) и расширен с помощью SCP.

Данные спецификации топологии могут быть отправлены напрямую в кластер Storm для выполнения через команду ***runspec***.

SCP.NET добавляет следующие функции для определения транзакционной топологии:

| Новые функции          | Параметры     | Описание                                                                                                                          |
|------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------|
| **tx-topolopy**        | topology-name 
                           spout-map     
                          bolt-map       | Определение транзакционной топологии по имени, карта для определения "воронки" и "сита".                                          |
| **scp-tx-spout**       | exec-name     
                          args           
                          fields         | Определение транзакционной "воронки". Запускает приложение с помощью ***exec-name***, используя ***args***                        
                                          . ***Поля*** — это поля вывода для "воронки".                                                                                      |
| **scp-tx-batch-bolt**  | exec-name     
                          args           
                          fields         | Определение транзакционного пакетного "сита". Запускает приложение с помощью ***exec-name***, используя ***args***                
                                          . Поля — это поля вывода для "воронки".                                                                                            |
| **scp-tx-commit-bolt** | exec-name     
                          args           
                          fields         | Определение транзакционного подтверждающего "сита" Запускает приложение с помощью ***exec-name***, используя args                 
                                          . ***Поля*** — это поля вывода для "воронки".                                                                                      |
| **nontx-topolopy**     | topology-name 
                           spout-map     
                          bolt-map       | Определение нетранзакционной топологии по имени, карта для определения "воронки" и "сита".                                        |
| **scp-spout**          | exec-name     
                          args           
                          fields         
                          parameters     | Определение нетранзакционной "воронки". Запускает приложение с помощью ***exec-name***, используя ***args***.                     
                                          Поля **** — это поля вывода для "воронки"                                                                                          
                                          . ***Параметры*** не являются обязательными. Используйте их для определения таких параметров, как "nontransactional.ack.enabled".  |
| **scp-bolt**           | exec-name     
                          args           
                          fields         
                          parameters     | Определение нетранзакционного "сита". Запускает приложение с помощью ***exec-name***, используя ***args***.                       
                                          Поля **** — это поля вывода для "сита"                                                                                             
                                          . ***Параметры*** не являются обязательными. Используются для определения таких параметров, как "nontransactional.ack.enabled".    |

Для SCP.NET определены следующие ключевые слова:

| Ключевые слова | Описание                                                                                    |
|----------------|---------------------------------------------------------------------------------------------|
| **:name**      | Определение имени топологии                                                                 |
| **:topology**  | Определение топологии с использованием функций, описанных выше, а также встроенных функций. |
| **:p**         | Определение подсказок по параллелизму для каждой из "воронок" и "сита".                     |
| **:config**    | Определение параметров конфигурации или обновление существующих параметров.                 |
| **:schema**    | Определение Схемы потока данных                                                             |

И часто используемые параметры:

| Параметр                           | Описание                                                             |
|------------------------------------|----------------------------------------------------------------------|
| **"plugin.name"**                  | Имя exe-файла для дополнительного модуля на C\#                      |
| **"plugin.args"**                  | Аргументы параметров дополнительного модуля                          |
| **"output.schema"**                | Схема вывода                                                         |
| **"nontransactional.ack.enabled"** | Если подтверждение обработки для нетранзакционной топологии активно. |

Команда "runspec" будет реализована вместе с пакетами BITS следующим образом:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Параметр ***resource-dir*** не является обязательным. Вам необходимо указать его значение, если вы хотите подключить приложение C\#. В этом каталоге будет находиться приложение, а также данные зависимостей и конфигураций.

Параметр ***classpath*** также не является обязательным. Он используется для указания пути к классу Java, если файл со спецификацией содержит "воронку" или "сито" на Java.

## Прочие характеристики

### Объявление схемы ввода и вывода

В процессе C\# пользователь может отправить кортеж данных, платформе необходимо провести сериализацию кортежа в поток байтов [], перевести данные на сторону Java, после чего Storm передаст кортеж данных в место назначения. В то же самое время в компоненте, находящемся далее по линии перемещения данных, процесс C\# получает кортеж данных со стороны Java и преобразует его в изначальный тип данных с помощью платформы. Данные действия производятся платформой в скрытом режиме.

Для поддержки процессов сериализации и десериализации код пользователя должен объявить схемы ввода и вывода.

Схема ввода и вывода потока — это словарь, ключом является значение Идентификатора потока, а значением — Тип колонки Можно объявить, что компонент поддерживает несколько потоков.

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

В объект Context у нас будут добавлены следующие прикладные программные интерфейсы (API):

     public void DeclareComponentSchema(ComponentStreamSchema schema)

Код пользователя должен гарантировать, что отправленные кортежи данных подчиняются схеме, заданной для этого потока. В противном случае система создаст исключение во время выполнения.

### Поддержка многопоточности.

SCP предоставляет пользовательскому коду возможность получения и отправки данных с помощью множества различных потоков одновременно. Эта возможность отражается в объекте Context, так как метод отправки получает оптимальный параметр идентификатора потока.

В объект SCP.NET Context были добавлены два метода. Они используются при отправке кортежа или кортежей данных для обозначения идентификатора потока. Идентификатор потока (StreamId) — это строка, которая должна одновременно соответствовать C\# и Спецификации определений топологии.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);  

Отправка в несуществующий поток приведет к созданию исключения во время выполнения.

### Объединение полей

Встроенные группирования полей в Strom работают неправильно в SCP.NET. На стороне Java Proxy все типы данных полей в настоящее время являются байтовыми, а эти группирования полей используют хэш-код байтового объекта для выполнения группирования. Хэш-код байтового объекта — это адрес этого объекта в памяти. Поэтому функция объединения будет работать неправильно для двух байтовых [] объектов, у которых одинаковое значение, но различные адреса расположения.

Для SCP.NET добавлен улучшенный метод объединения, который для объединения объектов в группы использует байтовые значения[]. Синтаксис файла **SPEC** выглядит следующим образом:

    (bolt-spec
        {
            “spout_test” (scp-field-group :non-tx [0,1])
        }
        …
    )  

В данном случае

1.  "scp-field-group" означает "настроенное группирование полей, реализованное SCP".
2.  ":tx" или "non-tx" означает транзакционную или нетранзакционную топологию. Эта информация полезна для нас, поскольку начальных индекс в топологиях ":tx" и "non-tx" отличается.
3.  [0,1] означает набор хэш-функций идентификаторов полей (начальное значение = "0").

### Гибридная топология

Изначально Storm написан на языке Java, а самой важной особенностью проекта SCP является предоставление нашим клиентам возможности использовать код на C\# для управления бизнес-логикой. Но мы также поддерживаем гибридные топологии, в которых возможны не только "воронки" и "сита" на C\#, но и на Java.

#### Задание "воронок" и "сит" на Java в файле спецификаций.

В файле спецификаций команды "scp-spout" и "scp-bolt" также можно использовать для указания "воронок " и "сит" на Java. Пример:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)  

"microsoft.scp.example.HybridTopology.Generator" — это имя класса "воронок" на Java.

#### Указание пути к классу Java в команде "runSpec "

Если вы хотите задать топологию с использованием "воронок" и "сит" на Java, сначала необходимо скомпилировать "воронки" или "сита" на Java и получить файлы jar. Затем, когда вы задаете топологию, нужно указать путь к классу Java, который содержит файлы jar. Пример:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  

"examples\\HybridTopology\\java\\target" — папка с файлом jar.

#### Сериализация или десериализация между Java и C

В гибридной топологии мы должны поддерживать сериализацию произвольных объектов на стороне Java и десериализацию на стороне C\#, и наоборот. **В настоящее время поддерживаются только Java Spout и C\# Bolt**, дальнейшие улучшения будут представлены в следующем выпуске.

Для начала нам нужно обеспечить стандартное применение сериализации на стороне Java и десериализации на стороне C\#. Метод сериализации на стороне Java можно задать через файл спецификаций:

      (scp-bolt
        {
          "plugin.name" "HybridTopology.exe"
          "plugin.args" ["displayer"]
          "output.schema" {}
          "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
        })  

Метод десериализации на стороне C\# можно задать через программный код пользователя:

    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
    inputSchema.Add("default", new List<Type>() { typeof(Person) });
    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
    this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

Стандартная сериализация должна работать должным образом в большинстве случаев, если тип данных не слишком сложный. В некоторых случаях пользователь может добавить свой тип реализации — либо из-за сложного типа данных, либо из-за того, что стандартная реализация не отвечает требованиям пользователя.

Интерфейс сериализации на стороне Java задается следующим образом:

    public interface ICustomizedInteropJavaSerializer {
        public void prepare(String[] args);
        public List<ByteBuffer> serialize(List<Object> objectList);
    }  

Интерфейс сериализации на стороне C\# задается следующим образом:

    public interface ICustomizedInteropCSharpDeserializer
    {
        List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
    }  

## Хост-режим SCP

В этом режиме пользователь может компилировать код в DLL-файл и использовать предоставляемый SCP файл SCPHost.exe, чтобы задать топологию. Соответствующий файл спецификации выглядит следующим образом:

        (scp-spout
          {
            "plugin.name" "SCPHost.exe"
            "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
            "output.schema" {"default" ["sentence"]}
          })

В данном случае в качестве "plugin.name" указан файл "SCPHost.exe", полученный из SCP SDK. SCPHost.exe принимает только три параметра:

1.  Первый из них — это имя DLL-файла. В данном примере "HelloWorld.dll"
2.  Второй — это имя класса. В данном примере это "Scp.App.HelloWorld.Generator".
3.  Третий — это общедоступный статический метод, который можно вызвать для того, чтобы получить экземпляр ISCPPlugin.

В хост-режиме код пользователя компилируется в DLL-файл и вызывается платформой SCP. Поэтому платформа SCP получает полный контроль над всей логической схемой обработки информации. Мы рекомендуем нашим клиентам задавать топологию через хост-режим SCP, поскольку это упрощает процесс программирования, способствует увеличению его гибкости, а также лучшей совместимости с предыдущими версиями.

## Примеры программирования SCP

### HelloWorld

HelloWorld — это очень простой пример, который показывает преимущества SCP.Net. В нем используется нетранзакционная топология c воронкой "Cоздатель" и двумя ситами, которые называются "Разделитель" и "Счетчик". Воронка "Создатель" случайным образом создает предложения и отправляет их на "Разделитель". "Разделитель" разбивает эти предложения на слова и отправляет их на "Счетчик". "Счетчик" использует словарь для записи частоты употребления каждого слова.

Для этого примера есть два файла спецификации — "HelloWorld.spec" и "HelloWorld\_EnableAck.spec". В программном коде C\# можно выяснить, задействована ли функция подтверждения, получив pluginConf со стороны Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);  

Если служба подтверждения задействована, то в "воронке" словарь используется для помещения неподтвержденных кортежей во временную память. Если вызывается Fail(), то кортежи данных с ошибками будут отсылаться повторно.

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### HelloWorldTx

Пример с программой HelloWorldTx демонстрирует, как реализовать транзакционную топологию. В ней "воронка" называется "создателем", пакетные "сита" называются "разделители-счетчики", а подтверждающее "сито" называется "счетчиком сумм". Также имеются три предварительно созданных текстовых файла: "DataSource0.txt", "DataSource1.txt" и "DataSource2.txt".

При каждой транзакции "Создатель" случайным образом выбирает два из трех предварительно созданных файла и отправляет имена двух этих файлов на "Разделитель-Счетчик". "Разделитель-Счетчик" сначала получает имя от пришедшего кортежа данных, затем открывает файлы и считывает количество слов в нем и, наконец, отправляет данные с количеством слов на "Счетчик сумм". "Счетчик сумм" выводит конечный результат подсчета.

Для достижения семантики "только однажды", подтверждающий "Счетчик сумм" должен определять перезапущенные транзакции. В этом примере транзакция имеет статическую переменную-член.

    public static long lastCommittedTxId = -1;  

После создания экземпляра ISCPBatchBolt транзакция получает статус txAttempt от входящих параметров:

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

После того как вызвана FinishBatch(), к транзакции будет добавлен параметр "lastCommittedTxId", если она не была перезапущена.

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
        … …
    }  

### Гибридная топология

Данная топология содержит "воронку" на Java и "сито" на C\#. В ней используются стандартные для платформы SCP процессы сериализации и десериализации. Сведения о файле спецификаций см. в "HybridTopology.spec" в папке "examples\\HybridTopology", сведения об указании пути к классу Java см. в "SubmitTopology.bat".

### Демо-версия SCPHost

Этот пример по своей сути не отличается от примера с программой HelloWorld. Единственное отличие заключается в том, что пользовательский код компилируется как DLL, а топология отправляется с помощью SCPHost.exe. Подробные пояснения см. в разделе "Режим хоста SCP".

  [0]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png

---
title: Руководство по программированию для SCP.NET | Документация Майкрософт
description: Узнайте, как использовать SCP.NET для создания топологий Storm на основе .NET для использования со Storm в HDInsight.
services: hdinsight
documentationcenter: ''
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 0f4c021bc209c99e1b3f34b34bf5ba0549eb48f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421561"
---
# <a name="scp-programming-guide"></a>Руководство по программированию для SCP
SPC — это платформа для создания надежного, согласованного и высокопроизводительного приложения для обработки данных в реальном времени. Она основана на [Apache Storm](http://storm.incubator.apache.org/) — системе обработки потоковых данных, созданной сообществами разработчиков OSS. Создателем Storm является Натан Марц (Nathan Marz), программа имеет открытый код и распространяется компанией Twitter. В платформе используется [Apache ZooKeeper](http://zookeeper.apache.org/)— еще один проект Apache для обеспечения высоконадежной распределенной координации и управления состоянием. 

С помощью проекта SPC удалось не только перенести Storm в Windows, но и добавить расширения и адаптировать его для работы в экосистеме Windows. Дополнения включают в себя возможности разработки .NET и библиотеки; адаптация заключается в возможности развертывания на основе Windows. 

Дополнения и адаптация выполнены таким образом, что нам не придется разветвлять проекты OSS, а можно использовать производные экосистемы, созданные на базе Storm.

## <a name="processing-model"></a>Модель обработки данных
Данные в SCP оформляются в виде постоянных потоков кортежей данных. Обычно кортежи оформляются в виде очереди на первом этапе, затем бизнес-логика приложения, находящегося внутри топологии Storm, подбирает и трансформирует их. Наконец, данные на выходе могут подаваться в виде кортежей в другую систему SCP или отправляться на хранение в системы распределенных файлов или базы данных, например SQL Server.

![Схема очереди, передающей данные для обработки, которая наполняет хранилище данных](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

В Storm вычислительные графы определяются топологией приложения. Каждый узел в топологии содержит логическую схему обработки информации, а по связям между узлами можно определить движение потока данных. Узлы, которые "вливают" входные данные в топологию, называются объектами _spout_ (воронками) и могут использоваться для создания последовательности данных. Входные данные могут располагаться в файлах журнала, транзакционной базе данных, системном счетчике производительности и т. д. Узлы для потоков входных и выходных данных называются объектами _bolt_ (ситами), они осуществляют фактическую фильтрацию, выборку и агрегацию данных.

SCP поддерживает максимальное качество работы при типе обработки данных "минимум однажды" и "только однажды". В распределенном приложении для обработки потоковой передачи могут возникать различные ошибки во время обработки данных, например сбой сети, аппаратный сбой, ошибка в коде пользователя и т. п. Обработка по принципу "минимум однажды" гарантирует, что все данные будут обрабатываться по крайней мере один раз благодаря автоматической повторной обработке тех же данных в случае ошибки. Модель обработки "минимум однажды" проста и надежна и отлично подходит для многих приложений. Но когда приложение требует точного подсчета данных, модель обработки "минимум однажды" не подходит, так как одни и те же данные могут проходить через топологию приложения несколько раз. Для подобных случаев разработан вид обработки "только однажды". Он обеспечивает достоверность итоговых данных даже в тех случаях, когда данные могут посылаться повторно и обрабатываться несколько раз подряд.

SCP позволяет разработчикам .NET создавать приложения для обработки данных в реальном времени со скрытым использованием возможностей Storm на виртуальных машинах Java. .NET и виртуальные машины Java обмениваются данными через локальные TCP-сокеты. По сути, каждая пара "воронка/сито" — это пара процессов .Net/Java, в которой логика пользователя задействована в процессе .Net в качестве дополнительного модуля.

Для создания приложения для обработки данных поверх SCP необходимо выполнить следующее:

* Создать и активировать "воронки" для извлечения данных из очереди.
* Создать и активировать элементы bolt для обработки входящих данных и сохранения данных после обработки во внешнем хранилище, например в базе данных.
* Создать топологию процесса, загрузить и запустить ее. Топология определяет вершины и поток данных между вершинами. SCP получает спецификацию топологии и размещает из в кластере Storm, где каждая вершина работает в отдельном логическом узле. Переход на резервный ресурс и масштабирование выполняет планировщик задач Storm.

В этой статье мы разберем несколько простых примеров создания приложения для обработки данных с помощью SCP.

## <a name="scp-plugin-interface"></a>Интерфейс подключаемого модуля SCP
Дополнительные модули (или приложения) SCP — это автономные файлы типа EXE, которые можно запускать в Visual Studio во время фазы разработки, а также можно подключать к конвейеру Storm после завершения разработки. Процесс написания кода дополнительного модуля SCP ничем не отличается от процессов написания кода для стандартных приложений в консоли Windows. Платформа SCP.NET объявляет интерфейс для "воронки" и "сита", которые пользователю нужно заполнить программным кодом для модуля. Это делается для того, чтобы пользователь мог сосредоточиться на своей задаче и предоставил платформе SCP.NET позаботиться обо всех остальных аспектах.

Код дополнительного модуля нужно внести в один из следующих интерфейсов, в зависимости от того, является ли топология транзакционной или нетранзакционной и является ли компонент объектом spout или bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin — это общий интерфейс для различных дополнительных модулей. В данном случае мы работаем с имитатором интерфейса.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout — это интерфейс для нетранзакционных "воронок".

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Когда вы вызываете `NextTuple()`, пользовательский код C\# может отправить один или несколько кортежей. Если данных для отправки не существует, то возвращение метода должно произойти без отправки данных. Нужно отметить, что команды `NextTuple()`, `Ack()` и `Fail()` вызываются в сплошном цикле однопоточной обработки процесса C\#. Если кортежей для отправки не существует, то разумно будет приостановить NextTuple на короткий промежуток времени (около 10 миллисекунд), чтобы излишне не нагружать ЦП.

Методы `Ack()` и `Fail()` вызываются только в случае активации механизма подтверждения в файле спецификации. `seqId` используется для определения подтверждения или отклонения принятия кортежа. Если подтверждение включено в нетранзакционной топологии, в "воронке" должны использоваться следующие функции отправки:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Если подтверждение не поддерживается нетранзакционной топологией, функции `Ack()` и `Fail()` можно не заполнять кодом.

Входной параметр `parms` в этих функциях представляет собой пустой словарь. Он зарезервирован для использования в будущем.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt — это интерфейс для нетранзакционных "сит"

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

При наличии нового кортежа для его обработки будет вызвана функция `Execute()`.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout — это интерфейс для транзакционных "воронок".

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Так же как и в случае с нетранзакционными воронками, команды `NextTx()`, `Ack()` и `Fail()` вызываются в сплошном цикле однопоточной обработки процесса C\#. Если данных для отправки не существует, то разумно будет приостановить `NextTx` на короткий промежуток времени (около 10 миллисекунд), чтобы излишне не нагружать ЦП.

`NextTx()` вызывается для того, чтобы начать новую передачу, для обозначения транзакции используется выходной параметр `seqId`, который также используется в командах `Ack()` и `Fail()`. В случае с `NextTx()`пользователь может отправлять данные в часть Java. Данные сохраняются в ZooKeeper для возможности провести повторную отсылку. Так как возможности хранения ZooKeeper ограничены, пользователи могут отправлять метаданные, но не могут отправлять данные большого объема в транзакционных объектах spout.

Storm автоматически повторит транзакцию, если она завершилась с ошибкой, поэтому в нормальных условиях `Fail()` вызывать не следует. Но так как платформа SCP может проверить метаданные от транзакционной "воронки", то она может вызвать `Fail()` , если метаданные неверны.

Входной параметр `parms` в этих функциях представляет собой пустой словарь. Он зарезервирован для использования в будущем.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt — это интерфейс для транзакционных "сит".

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` вызывается при наличии нового кортежа, поступающего в "сито". `FinishBatch()` вызывается после завершения этой транзакции. Входной параметр `parms` зарезервирован для использованию в будущем.

Для транзакционной топологии существует важная концепция — `StormTxAttempt`. Она содержит два поля: `TxId` и `AttemptId`. `TxId` используется для определения специальной транзакции, и для заданной транзакции может предприниматься несколько попыток, если перед этим она завершилась ошибкой и была повторена. SCP.NET создает объект ISCPBatchBolt для обработки каждой попытки `StormTxAttempt` точно так же, как это делает Storm в Java. Это делается для поддержки параллельной обработки данных. Пользователю необходимо помнить, что после завершения попытки выполнения транзакции объект ISCPBatchBolt будет уничтожен и удален как мусор.

## <a name="object-model"></a>Объектная модель
SCP.NET предоставляет разработчикам простой набор ключевых объектов для программирования. В их число входят: **Context**, **StateStore** и **SCPRuntime**. О них речь идет в оставшейся части раздела.

### <a name="context"></a>Context
Context обеспечивает рабочую среду для приложений. Каждому экземпляру дополнительного модуля ISCP (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) соответствует экземпляр объекта Context. Функциональные возможности, предоставляемые Context, можно разделить на две части: статическая часть, доступная во всем процессе C\#, и динамическая часть, доступная только для определенного экземпляра Context.

### <a name="static-part"></a>Статическая часть
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` отвечает за создание записей в журнале.

`pluginType` используется для указания типа подключаемого модуля в процессе C\#. Если процесс C`SCP_NET_LOCAL` запущен в локальном тестовом режиме (без Java), используется подключаемый модуль типа \#.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` предназначен для получения параметров конфигурации из части Java. Параметры передаются со стороны Java после запуска подключаемого модуля на C\#. Параметры `Config` делятся на две части: `stormConf` и `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` — это параметры, определенные Storm, а `pluginConf` — это параметры, определенные SCP. Например: 

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` предоставляется для получения контекста топологии и особенно полезен для компонентов с поддержкой множественного параллелизма. Вот пример: 

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

### <a name="dynamic-part"></a>Динамическая часть
Следующие интерфейсы привязаны к определенным экземплярам объекта Context. Экземпляр объекта Context создается платформой SCP.NET и передается в код пользователя.

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Для нетранзакционных "воронок" с поддержкой функции подтверждения обработки данных предусмотрен следующий метод:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Для нетранзакционных "сит" с поддержкой функции подтверждения получения данных после принятия кортежа возможны только две команды: `Ack()` и `Fail()`. При отправке нового кортежа данных "сита" также должны указывать привязки для него. Существуют следующие методы.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` предоставляет службы метаданных, создание монотонных последовательностей и координацию без ожидания. Распределенные многозадачные абстракции высокого уровня могут создаваться на базе `StateStore`, включая создание распределенных блокировок, распределенных очередей, барьеров и служб транзакций.

Приложения SCP могут использовать объект `State` для сохранения определенной информации в ZooKeeper, особенно в транзакционной топологии. Если во время этого процесса в транзакционной "воронке" происходит сбой и перезагрузка, вся необходимая информация может быть получена из ZooKeeper, и конвейер данных будет восстановлен.

Объект `StateStore` в основном обладает следующими методами.

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

Объект `State` в основном обладает следующими методами.

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

Для метода `Commit()`, когда simpleMode имеет значение true, соответствующий ZNode будет удален из ZooKeeper. В противном случае будет удален текущий ZNode и создан новый в COMMITTED\_PATH.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime предоставляет два метода:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` используется для инициализации среды выполнения SCP. В этом методе процесс C\# соединяется со стороной Java и извлекает параметры конфигурации и контекст топологии.

`LaunchPlugin()` используется для запуска цикла обработки сообщений. В этом цикле подключаемый модуль C\# получает сообщения со стороны Java (включая кортежи данных и управляющие сигналы), а затем обрабатывает их, возможно, вызывая метод интерфейса, содержащийся в коде пользователя. Входной параметр для метода `LaunchPlugin()` является делегатом, который может возвращать объект, реализующий интерфейс ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Для ISCPBatchBolt мы можем получить `StormTxAttempt` из `parms` и определить, имела ли место повторная попытка. Этот процесс проверки обычно происходит в подтверждающем объекте bolt. Ознакомьтесь с примером `HelloWorldTx`.

Как правило, дополнительный модуль SCP в такой ситуации может работать в двух режимах:

1. Режим локальной проверки. В этом режиме подключаемые модули SCP (с кодом пользователя на языке C\#) активны в Visual Studio во время фазы разработки. В этом режиме можно использовать параметр `LocalContext`, который указывает метод для сериализации отправленных кортежей в локальные файлы и выполняет обратное считывание в память.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Обычный режим. В этом режиме подключаемые модули SCP запускаются процессом Java Storm.
   
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

## <a name="topology-specification-language"></a>Язык спецификации топологии
Спецификация топологии SCP представляет собой доменный язык для описания и настройки топологий SCP. Он основан на Clojure DSL для Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) и расширен с помощью SCP.

Спецификации топологии можно отправить напрямую в кластер Storm для выполнения через команду ***runspec***.

В SCP.NET добавлены следующие функции для определения транзакционных топологий:

| **Новые функции** | **Параметры** | **Описание** |
| --- | --- | --- |
| **tx-topolopy** |topology-name<br />spout-map<br />bolt-map |Определение транзакционной топологии по имени, &nbsp;карта для определения воронки и сита. |
| **scp-tx-spout** |exec-name<br />args<br />fields |Определение транзакционной "воронки". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для воронки. |
| **scp-tx-batch-bolt** |exec-name<br />args<br />fields |Определение транзакционного пакетного "сита". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />fields — это поля вывода для "воронки". |
| **scp-tx-commit-bolt** |exec-name<br />args<br />fields |Определение транзакционного подтверждающего объекта bolt. Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для сита. |
| **nontx-topolopy** |topology-name<br />spout-map<br />bolt-map |Определение нетранзакционной топологии по имени, &nbsp;карта для определения воронки и сита. |
| **scp-spout** |exec-name<br />args<br />fields<br />parameters |Определение нетранзакционной "воронки". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для воронки.<br /><br />Параметр ***parameters*** необязательный и используется для указания некоторых параметров, например nontransactional.ack.enabled. |
| **scp-bolt** |exec-name<br />args<br />fields<br />parameters |Определение нетранзакционного "сита". Запускает приложение с именем ***exec-name*** и аргументами ***args***.<br /><br />***fields*** — это поля вывода для сита.<br /><br />Параметр ***parameters*** необязательный и используется для указания некоторых параметров, например nontransactional.ack.enabled. |

Для SCP.NET определены следующие ключевые слова:

| **Ключевые слова** | **Описание** |
| --- | --- |
| **:name** |Определение имени топологии |
| **:topology** |Определение топологии с использованием приведенных выше функций, а также встроенных функций. |
| **:p** |Определение подсказок по параллелизму для каждой из "воронок" и "сита". |
| **:config** |Определение параметров конфигурации или обновление существующих параметров. |
| **:schema** |Определение Схемы потока данных |

Часто используемые параметры:

| **Параметр** | **Описание** |
| --- | --- |
| **"plugin.name"** |Имя exe-файла для дополнительного модуля на C# |
| **"plugin.args"** |Аргументы параметров дополнительного модуля |
| **"output.schema"** |Схема вывода |
| **"nontransactional.ack.enabled"** |Если подтверждение обработки для нетранзакционной топологии активно. |

Команда "runspec" запускается вместе с ключами и выглядит это так:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Параметр ***resource-dir*** необязательный. Его необходимо указывать, если нужно подключить приложение C\#. В этом каталоге находится это приложение, его зависимости и настройки.

Параметр ***classpath*** также необязательный. Он используется для указания пути к классу Java, если файл со спецификацией содержит "воронку" или "сито" на Java.

## <a name="miscellaneous-features"></a>Прочие функции
### <a name="input-and-output-schema-declaration"></a>Объявление схемы ввода и вывода
В процессе C\# пользователи могут отправить кортеж. Платформе необходимо будет сериализовать этот кортеж в байты, передать его в часть Java, после чего Storm передаст его целевым объектам. Тем временем в нижестоящих компонентах процесс C\# получит кортеж данных со стороны Java и преобразует его в изначальный тип данных с помощью платформы. Все эти действия производятся платформой в скрытом режиме.

Для поддержки процессов сериализации и десериализации код пользователя должен объявить схемы ввода и вывода.

Схема потока ввода-вывода — это словарь. Ключом является значение идентификатора потока, а значением — тип столбца. Можно объявить, что компонент поддерживает несколько потоков.

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

Разработчики должны убедиться, что отправленные кортежи данных подчиняются схеме, заданной для этого потока. В противном случае система создаст исключение среды выполнения.

### <a name="multi-stream-support"></a>Поддержка многопоточности
SCP предоставляет пользовательскому коду возможность получения и отправки данных с помощью множества различных потоков одновременно. Эта возможность отражается в объекте Context, так как метод отправки получает оптимальный параметр идентификатора потока.

В объект SCP.NET Context были добавлены два метода. Они используются при отправке кортежа или кортежей данных для обозначения идентификатора потока. StreamId — это строка, которая должна одновременно соответствовать C\# и спецификации определения топологии.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Отправка в несуществующий поток приведет к возникновению исключения во время выполнения.

### <a name="fields-grouping"></a>Группирование полей
Встроенная в Storm функция группирования полей не работает должным образом в SCP.NET. На стороне прокси-сервера Java все поля представлены потоком байтов [], в то время как функция объединения полей использует для объединения хэш-код объекта в виде потока байтов[]. Хэш-код байтового объекта — это адрес этого объекта в памяти. Поэтому функция объединения будет работать неправильно для двух байтовых [] объектов, у которых одинаковое значение, но различные адреса расположения.

В SCP.NET добавлен настраиваемый метод объединения, который для объединения полей в группы использует содержимое byte[]. Синтаксис файла **SPEC** выглядит следующим образом:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


В данном случае

1. "scp-field-group" означает "адаптированный метод группирования полей с помощью SCP".
2. ":tx" или "non-tx" означает транзакционную или нетранзакционную топологию. Эта информация полезна для нас, поскольку начальных индекс в топологиях ":tx" и "non-tx" отличается.
3. [0,1] означает набор хэш-функций идентификаторов полей (начальное значение — 0).

### <a name="hybrid-topology"></a>Гибридная топология
Изначально система Storm написана на языке Java. На платформе SCP.NET эта система была усовершенствована, чтобы разработчики C\# могли писать код C\# для обработки бизнес-логики. Но эта система также поддерживает гибридные топологии, в которых возможны элементы spout и bolt не только на C\#, но и на Java.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Задание "воронок" и "сит" на Java в файле спецификаций.
В файле спецификаций команды "scp-spout" и "scp-bolt" также можно использовать для указания "воронок " и "сит" на Java. Пример:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Здесь `microsoft.scp.example.HybridTopology.Generator` является именем класса "воронки" Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Указание classpath Java в команде runSpec
Если вы хотите отправить топологию с "воронками" и "ситами" Java, сначала необходимо скомпилировать "воронки" или "сита" Java и получить JAR-файлы. Затем при отправке топологии нужно указать classpath Java, который содержит JAR-файлы. Вот пример: 

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

**examples\\HybridTopology\\java\\target\\** — папка с JAR-файлом воронки или сита Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Сериализация или десериализация между Java и C\#
Компонент SCP включает в себя часть C\# и часть Java. Чтобы обеспечить взаимодействия с собственными воронками и ситами, Java нужно выполнять сериализацию и десериализацию между частями Java и C\#, как показано на следующей схеме.

![Схема компонента Java, отправляющего данные в компонент SCP, который отправляет данные в компонент Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Сериализация на стороне Java и десериализация на стороне C\#**
   
   Для начала нужно обеспечить стандартное применение сериализации на стороне Java и десериализации на стороне C\#. Метод сериализации на стороне Java можно задать через файл спецификаций:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Метод десериализации на стороне C\# можно задать через программный пользовательский код C\#.
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Стандартная реализация должна работать должным образом в большинстве случаев при условии, что тип данных не слишком сложен. В некоторых случаях пользователи могут добавить свой тип реализации — либо из-за сложного типа данных, либо из-за того, что стандартная реализация не отвечает требованиям пользователя.
   
   Интерфейс сериализации на стороне Java задается следующим образом:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Интерфейс сериализации на стороне C\# задается следующим образом.
   
   Общедоступный интерфейс ICustomizedInteropCSharpDeserializer.
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Сериализация на стороне C\# и десериализация на стороне Java**
   
   Метод десериализации на стороне C\# следует указать в пользовательском коде C\#.
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Метод десериализации в части Java следует указать в SPEC-файле.
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Здесь microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer — имя десериализатора, а microsoft.scp.example.HybridTopology.Person — целевой класс, в который десериализуются данные.
   
   Пользователь может также подключить свою собственную реализацию сериализатора C\# и десериализатора Java. Код ниже — это интерфейс для сериализатора C\#.
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Код ниже — это интерфейс для десериализатора Java.
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Хост-режим SCP
В этом режиме пользователь может компилировать код в DLL-файл и использовать предоставляемый SCP файл SCPHost.exe, чтобы задать топологию. Соответствующий файл спецификации выглядит следующим образом:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Здесь в качестве `plugin.name` указан `SCPHost.exe` из пакета SDK для SCP. Файлу SCPHost.exe требуется три параметра:

1. Первый из них — это имя библиотеки DLL. В данном примере это `"HelloWorld.dll"`.
2. Второй — это имя класса. В данном примере это `"Scp.App.HelloWorld.Generator"`.
3. Третий — это общедоступный статический метод, который можно вызвать для того, чтобы получить экземпляр ISCPPlugin.

В хост-режиме код пользователя компилируется в DLL-файл и вызывается платформой SCP. Поэтому платформа SCP получает полный контроль над всей логической схемой обработки информации. Мы рекомендуем нашим клиентам задавать топологию через хост-режим SCP, поскольку это упрощает процесс программирования, способствует увеличению его гибкости, а также лучшей совместимости с предыдущими версиями.

## <a name="scp-programming-examples"></a>Примеры программирования для SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** — это простой пример, на котором можно продемонстрировать удобство работы с SCP.NET. Она использует нетранзакционную топологию, в которой воронка называется **generator**, а два сита — **splitter** и **counter**. Объект spout с именем **generator** случайным образом создает предложения и отправляет их в объект bolt c именем **splitter**. Объект bolt c именем **splitter** разбивает эти предложения на слова и отправляет их в объект bolt c именем **counter**. "Сито" "counter" использует словарь для записи частоты употребления каждого слова.

Для этого примера существует два файла спецификации — **HelloWorld.spec** и **HelloWorld\_EnableAck.spec**. В программном коде C\# можно определить, задействована ли функция подтверждения, получив pluginConf со стороны Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Если служба подтверждения задействована, то в "воронке" словарь используется для помещения неподтвержденных кортежей во временную память. Если вызывается Fail(), то отклоненный кортеж отсылается повторно.

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

### <a name="helloworldtx"></a>HelloWorldTx
Пример **HelloWorldTx** демонстрирует, как реализовать транзакционную топологию. Он содержит объект spout с именем **generator**, пакетный объект bolt c именем **partial-count** и объект bolt подтверждения c именем **count-sum**. В примере содержатся три предварительно созданных текстовых файла: **DataSource0.txt**, **DataSource1.txt** и **DataSource2.txt**.

При каждой транзакции объект spout c именем **generator** случайным образом выбирает два из трех предварительно созданных файла и отправляет их имена в объект bolt c именем **partial-count**. Объект bolt с именем **partial-count** сначала извлекает имя файла из полученного кортежа, открывает этот файл и считает количество слов в нем, после чего отправляет полученное значение в объект bolt с именем **count-sum**. Объект bolt с именем **count-sum** вычисляет итоговое количество.

Для достижения семантики **только однажды** подтверждающее сито **count-sum** должно определить перезапущенные транзакции. В этом примере транзакция имеет статическую переменную-член.

    public static long lastCommittedTxId = -1; 

После создания экземпляра ISCPBatchBolt он извлекает `txAttempt` из входящих параметров.

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

После вызова `FinishBatch()` параметр `lastCommittedTxId` обновляется, если транзакция не была перезапущена.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>Гибридная топология
Данная топология содержит воронку на Java и сито на C\#. В ней используются стандартные для платформы SCP процессы сериализации и десериализации. Сведения о файле спецификаций см. в файле **HybridTopology.spec** в папке **examples\\HybridTopology**, а сведения о том, как задавать путь к классу Java, — в файле **SubmitTopology.bat**.

### <a name="scphostdemo"></a>SCPHostDemo
Этот пример по своей сути не отличается от примера с программой HelloWorld. Единственное отличие заключается в том, что пользовательский код компилируется в DLL-файл и топология задается через файл SCPHost.exe. Дополнительная информация содержится в разделе "Хост-режим SCP".

## <a name="next-steps"></a>Дальнейшие действия
Примеры топологий Storm, созданных с помощью SCP, приведены в следующих документах.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Обработка событий из концентраторов событий Azure с помощью Storm в HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Обработка данных с датчиков автомобиля из концентраторов событий с использованием Storm в HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extract, Transform, and Load (ETL) from Azure Event Hubs to HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Analyze real-time Twitter sentiment with HBase in HDInsight | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight

Узнайте, как проводить [анализ мнений][анализ мнений] в режиме реального времени на основе данных большого размера с помощью HBase в кластере HDInsight (Hadoop).


Социальные веб-сайты являются одной из основных движущих сил для внедрения данных большого размера. Общедоступные API, предоставляемые сайтами, такими как Twitter, — полезный источник данных для анализа и понимания популярных тенденций. В этом учебнике вы создадите консольное приложение-службу, работающее в потоковом режиме, и веб-приложение ASP.NET для выполнения следующих задач:

![][]

- получение твитов с геотегами в режиме реального времени с помощью API потоковой передачи Twitter;
- оценка мнений на основе этих твитов;
- сохранение информации о мнениях в HBase с помощью пакета Microsoft HBase SDK;
- отображение статистических результатов в режиме реального времени на картах Bing с помощью веб-приложения ASP.NET. Визуальное представление твитов будет выглядеть примерно так:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	You will be able to query tweets with certain keywords to get a sense of the expressed opinion in tweets is positive, negative, or neutral.

Пример готового решения Visual Studio можно найти на странице по адресу [][]<https://github.com/maxluk/tweet-sentiment></a>.






























## Содержание

- [Предварительные требования][Предварительные требования]
- [Создание приложения Twitter][Создание приложения Twitter]
- [Создание простой службы потоковой передачи данных Twitter][Создание простой службы потоковой передачи данных Twitter]
- [Создание веб-сайта Azure для визуализации мнений пользователей Twitter][Создание веб-сайта Azure для визуализации мнений пользователей Twitter]
- [Дальнейшие действия][Дальнейшие действия]

## <a id="prerequisites"></a> Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- **Кластер HBase в HDInsight**. Инструкции по подготовке кластера см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][Приступая к использованию HBase с Hadoop в HDInsight]. Для выполнения учебника необходимы следующие данные:

	<table border="1">
	<tr><th>Свойство кластера</th><th>Описание</th></tr>
	<tr><td>Имя кластера HBase</td><td>Это имя вашего кластера HBase в HDInsight. Например: https://myhbase.azurehdinsight.net/</td></tr>
	<tr><td>Имя пользователя кластера</td><td>Имя учетной записи пользователя Hadoop. Имя пользователя Hadoop по умолчанию — <strong>admin</strong>.</td></tr>
	<tr><td>Пароль пользователя кластера</td><td>Пароль пользователя кластера Hadoop.</td></tr>
	</table>

- **Рабочая станция**, на которой установлено программное обеспечение Visual Studio 2013. Инструкции см. в разделе [Установка Visual Studio][Установка Visual Studio].





## <a id="twitter"></a> Создание идентификатора и секретов приложения Twitter

В интерфейсах API потоковой передачи Twitter для авторизации запросов используется протокол [OAuth][OAuth].

Первый шаг для начала использования OAuth состоит в создании нового приложения на сайте разработчиков Twitter.

**Создание идентификатора и секретов приложения Twitter**

1.  Войдите на веб-сайт [][1]<https://apps.twitter.com/></a>. Перейдите по ссылке **Войти сейчас**, если у вас нет учетной записи Twitter.
2.  Щелкните **Создать новое приложение**.
3.  Введите **Имя**, **Описание**, **Веб-сайт**. Поле "Веб-сайт" на самом деле не используется. В нем не обязательно указывать действительный URL-адрес. В следующей таблице приведены некоторые примеры значений:

	<table border="1">
	<tr><th>Поле</th><th>Значение</th></tr>
	<tr><td>Имя</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Описание</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Веб-сайт</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
	</table>

4. Установите значок **Да, я согласен** и нажмите кнопку **Создать приложение Twitter**.
5. Выберите вкладку **Разрешения**. Разрешение по умолчанию: **Только для чтения**. Этого разрешения достаточно для данного учебника.
6. Щелкните вкладку **Ключи API**.
7. Нажмите кнопку **Создать маркер доступа**.
8. Нажмите кнопку **Проверить OAuth** в правом верхнем углу страницы.
9. Запишите значения параметров **API key** (Ключ API), **API secret** (Секрет API), **Access token** (Маркер доступа) и **Access token secret** (Секрет маркера доступа). Они потребуются позже в данном руководстве.

    ![hdi.hbase.twitter.sentiment.twitter.app][hdi.hbase.twitter.sentiment.twitter.app]






























## <a id="streaming"></a> Создание простой службы потоковой передачи данных Twitter

Создайте консольное приложение для получения твитов, расчета оценки мнений на их основе и отправки обработанных слов из твитов в HBase.

**Создание решения Visual Studio**

1. Откройте **Visual Studio**.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. Введите или выберите следующие значения:

    - Шаблоны: **Visual C#**
    - Шаблон: **Консольное приложение**
    - Имя: **TweetSentimentStreaming**
    - Расположение: **C:\Tutorials**
    - Имя решения: **TweetSentimentStreaming**

4. Чтобы продолжить, нажмите кнопку **ОК**.



**Установка пакетов Nuget и добавление ссылок SDK**

1. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**. В нижней части страницы откроется консольная панель.
2. Чтобы установить пакет [Tweetinvi][Tweetinvi], используемый для доступа к API Twitter, и пакет [Protobuf-net][Protobuf-net], который служит для сериализиции и десериализации объектов, выполните следующие команды:

		Install-Package TweetinviAPI
		Install-Package protobuf-net 

	> [WACOM.NOTE] На 26 августа 2014 г. пакет Microsoft Hbase SDK Nuget был недоступен. Репозиторий Github — [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Пока этот пакет SDK не станет доступен, выполнять сборку библиотеки DLL необходимо самостоятельно. Инструкции см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][Приступая к использованию HBase с Hadoop в HDInsight].

3. В **обозревателе решений** щелкните правой кнопкой мыши папку **Ссылки** и выберите пункт **Добавить ссылку**.
4. В левой области разверните узел **Сборки**, а затем щелкните **Платформа**.
5. В правой области установите флажок перед пунктом **System.Configuration** и нажмите кнопку **ОК**.



**Определение класса службы для потоковой передачи данных Twitter**

1. В **обозревателе решений** щелкните правой кнопкой мыши элемент **TweetSentimentStreaming**, наведите указатель на пункт **Добавить** и щелкните **Класс**.
2. В поле **Имя** введите **HBaseWriter** и нажмите кнопку **Добавить**.
3. В начало файла **HBaseWriter.cs** добавьте следующие операторы using:

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. Добавьте в файл **HbaseWriter.cs** новый вызов класса **DictionaryItem**:

	    public class DictionaryItem
	    {
	        public string Type { get; set; }
	        public int Length { get; set; }
	        public string Word { get; set; }
	        public string Pos { get; set; }
	        public string Stemmed { get; set; }
	        public string Polarity { get; set; }
	    }

	Эта структура класса используется для анализа файла словаря мнений. На основе данных рассчитывается оценка мнений для каждого твита.

5. В классе **HBaseWriter** определите следующие константы и переменные:

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;
        
        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. Задайте значения констант, включая **<HBaseClusterName>**, **<HadoopUserName>** и **<HaddopUserPassword>**. Если вы хотите изменить имя таблицы HBase, вам необходимо соответствующим образом изменить имя таблицы в веб-приложении.

    Вы загрузите файл dictionary.tsv и переместите его в нужную папку позднее в этом учебнике.

7.  Определите следующие функции внутри класса **HBaseWriter**:

		// This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // create the HBase table if it doesn't exist
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Load sentiment dictionary from a file
            LoadDictionary();

			// Start a thread for writting to HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enqueue the Tweets received
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Load sentiment dictionary from a file
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calculate sentiment score
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

		// Popular a CellSet object to be written into HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Split the Tweet into words
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calculate sentiment score base on the words
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // For each word in the Tweet add a row to the HBase table
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Create a row
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Write a Tweet (CellSet) to HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Write the Tweet by words cell set to the HBase table
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

	Этот код обеспечивает следующие функциональные возможности:

    - **Подключение к Hbase [HBaseWriter()]**. Используйте пакет HBase SDK для создания объекта *ClusterCredentials* с URL-адресом кластера и учетными данными пользователя Hadoop, а затем создайте с его помощью объект *HBaseClient*.
    - **Создание таблицы HBase [HBaseWriter()]**. Используйте вызов метода *HBaseClient.CreateTable()*.
    - **Запись в таблицу HBase [WriterThreadFunction()]**. Используйте вызов метода *HBaseClient.StoreCells()*.

**Подготовка файла Program.cs**

1. В **обозревателе решений** дважды щелкните файл **Program.cs**, чтобы открыть его.
2. Добавьте в начало файла следующие операторы using:

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. В классе **Program** определите следующие константы:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. Установите значения констант в соответствии со значениями в приложении Twitter.

5. Измените функцию **Main()** так, чтобы она выглядела следующим образом:

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

6. Добавьте в класс следующую функцию:

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // Write Tweets to HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }

**Загрузка файла словаря мнений**

1. Перейдите на страницу [][]<https://github.com/maxluk/tweet-sentiment></a>.
2. Нажмите кнопку **Download ZIP** (Загрузить ZIP-файл).
3. Распакуйте архив на локальном компьютере.
4. Скопируйте файл **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5.  Вставьте файл в решение по пути **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv**.

**Запуск службы потоковой передачи**

1. В Visual Studio нажмите клавишу **F5**. Вот снимок экрана с консольным приложением:

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Оставьте консольное приложение потоковой передачи запущенным, пока вы занимаетесь разработкой веб-приложения. Это даст возможность собрать больше данных.



























## <a id="web"></a> Создание веб-сайта Azure для визуализации мнений пользователей Twitter

Этот подраздел посвящен созданию веб-приложения ASP.NET MVC, предназначенному для чтения мнений из HBase в режиме реального времени и отображения данных на картах Bing.

**Создание веб-приложения ASP.NET MVC**

1. Откройте Visual Studio.
2. В меню **Файл** выберите пункт **Создать**, а затем щелкните **Проект**.
3. Введите следующие данные:

    - Категория шаблона: **Visual C#/Веб-сайт**
    - Шаблон: **Веб-приложение ASP.NET**
    - Имя: **TweetSentimentWeb**
    - Расположение: **C:\Tutorials**
4. Нажмите кнопку **ОК**.
5. В разделе **Выбор шаблона** щелкните **MVC**.
6. В **Microsoft Azure** щелкните **Manage Subscriptions** (Управление подписками).
7. В окне **Manage Microsoft Azure Subscriptions** (Управление подписками Microsoft Azure) щелкните **Sign in** (Вход).
8. Введите свои учетные данные для Azure. Сведения о вашей подписке Azure появятся на вкладке Accounts (Учетные записи).
9. Нажмите кнопку **Close** (Закрыть), чтобы закрыть окно Manage Microsoft Azure Subscriptions.
10. В окне **New ASP.NET Project - TweetSentimentWeb** (Новый проект ASP.NET — TweetSentimentWeb) нажмите кнопку **ОК**.
11. В окне **Configure Microsoft Azure Site Settings** (Настройка параметров сайта Microsoft Azure) выберите ближайший к вам регион (параметр **Region**). Указывать сервер базы данных не нужно.
12. Нажмите кнопку **ОК**.

**Установка пакетов Nuget**

1. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**. В нижней части страницы откроется консольная панель.
2. Чтобы установить пакет [Protobuf-net][Protobuf-net], используемый для сериализиции и десериализации объектов, используйте следующую команду:

        Install-Package protobuf-net 

    > [WACOM.NOTE] На 20 августа 2014 г. пакет Microsoft Hbase SDK Nuget был недоступен. Репозиторий Github — [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Пока этот пакет SDK не станет доступен, выполнять сборку библиотеки DLL необходимо самостоятельно. Инструкции см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][Приступая к использованию HBase с Hadoop в HDInsight].

**Добавление класса HBaseReader**

1. В **обозревателе решений** разверните элемент **TweetSentiment**.
2. Щелкните правой кнопкой мыши элемент **Модели**, выберите пункт **Добавить** и щелкните **Класс**.
3. В поле "Имя" введите **HBaseReader.cs** и нажмите **Добавить**.
4. Замените код на приведенный ниже:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // For reading Tweet sentiment data from HDInsight HBase
		        HBaseClient client;
		
		        // HDinsight HBase cluster and HBase table information
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // The constructor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Query Tweets sentiment data from the HBase table asynchronously 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demonstrate Filtering the data from the past 6 hours the row key
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // Make async scan call
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // find the cell with string pattern "d:coor" 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

5. В классе **HBaseReader** измените значения констант:

    - **CLUSTERNAME**: имя кластера HBase, например *https://<HBaseClusterName>.azurehdinsight.net/*.
    - **HADOOPUSERNAME**: имя пользователя Hadoop в кластере HBase. Имя по умолчанию — *admin*.
    - **HADOOPUSERPASSWORD**: пароль пользователя Hadoop в кластере HBase.
    - **HBASETABLENAME** = "tweets\_by\_words".

    Имя таблицы HBase — tweets\_by\_words. Значения должны совпадать со значениями, отправленными в службу потоковой передачи, чтобы веб-приложение считывало данные из той же таблицы HBase.




**Добавление контроллера TweetsController**

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**.
2. Щелкните правой кнопкой мыши элемент **Контроллеры**, выберите пункт **Добавить**, а затем щелкните **Контроллер**.
3. Щелкните элемент **Web API 2 Controller - Empty** (Контроллер веб-интерфейса API 2 — пустой) и нажмите кнопку **Добавить**.
4. В поле имени контроллера введите **TweetsController** и нажмите кнопку **Добавить**.
5. В **обозревателе решений** дважды щелкните файл TweetsController.cs, чтобы открыть его.
6. Измените файл так, чтобы он выглядел следующим образом:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController : ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**Добавление файла heatmap.js**

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**.
2. Щелкните правой кнопкой мыши элемент **Скрипты**, выберите пункт **Добавить** и щелкните **Файл JavaScript**.
3. В поле имени элемента введите **heatmap.js**.
4. Скопируйте приведенный ниже код и вставьте его в файл. Код был создан Аластером Эйтчисоном (Alastair Aitchison). Дополнительные сведения см. на странице [][3]<http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/></a>.

		/*******************************************************************************
		* Author: Alastair Aitchison
		* Website: http://alastaira.wordpress.com
		* Date: 15th April 2011
		* 
		* Description: 
		* This JavaScript file provides an algorithm that can be used to add a heatmap
		* overlay on a Bing Maps v7 control. The intensity and temperature palette
		* of the heatmap are designed to be easily customisable.
		* 
		* Requirements:
		* The heatmap layer itself is created dynamically on the client-side using
		* the HTML5 <canvas> element, and therefore requires a browser that supports
		* this element. It has been tested on IE9, Firefox 3.6/4 and 
		* Chrome 10 browsers. If you can confirm whether it works on other browsers or
		* not, I'd love to hear from you!

		* Usage:
		* The HeatMapLayer constructor requires:
		* - A reference to a map object
		* - An array or Microsoft.Maps.Location items
		* - Optional parameters to customise the appearance of the layer
		*  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
        *
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Private Properties */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Set default options
		    var _options = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 1000,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'meters',
		
		        // Colour temperature gradient of the map
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Blue
		            "0.50": 'rgba(0,255,0,80)',    // Green
		            "0.75": 'rgba(255,255,0,120)', // Yellow
		            "1.00": 'rgba(255,0,0,150)'    // Red
		        },
		
		        // Callback function to be fired after heatmap layer has been redrawn 
		        callback: null
		    };
		
		    /* Private Methods */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Create the canvas element
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Override defaults with any options passed in the constructor
		            _setOptions(options);
		
		            // Load array of location data
		            _setPoints(locations);
		
		            // Create a colour gradient from the suppied colourstops
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Wire up the event handler to redraw heatmap canvas
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Resets the heat map
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Creates a colour gradient from supplied colour stops on initialisation
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // Applies a colour gradient to the intensity map
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // get the alpha of this pixel
		            if (a != 0) { // If there is any data to plot
		                pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
		                pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
		                pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
		            }
		            p += 4; // Move on to the next pixel
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Sets any options passed in
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Sets the heatmap points from an array of Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Main method to draw the heatmap
		    function _createHeatMap() {
		        // Ensure the canvas matches the current dimensions of the map
		        // This also has the effect of resetting the canvas
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calculate the pixel radius of each heatpoint at the current map zoom
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Convert lat/long to pixel location
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Create the Intensity Map by looping through each location
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Create radial gradient centred on this point
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Draw the heatpoint onto the canvas
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Apply the specified colour gradient to the intensity map
		        _colouriseHeatMap();
		
		        // Call the callback function, if specified
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Public Methods */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // Sets options for intensity, radius, colourgradient etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
		    this.SetPoints = function (locations) {
		        // Reset the existing heatmap layer
		        _clearHeatMap();
		        // Pass in the new set of locations
		        _setPoints(locations);
		        // Recreate the layer
		        _createHeatMap();
		    }
		
		    // Removes the heatmap layer from the DOM
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // Call the initialisation routine
		    _init();
		};
		
		// Call the Module Loaded method
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**Добавление файла tweetStream.js**

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**.
2. Щелкните правой кнопкой мыши элемент **Скрипты**, выберите пункт **Добавить** и щелкните **Файл JavaScript**.
3. В поле имени элемента введите **twitterStream.js**.
4. Скопируйте следующий код и вставьте его в файл:

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Initialize the map
		    var options = {
		        credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Heatmap options for positive, neutral and negative layers
		
		    var heatmapOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Register and load the Client Side HeatMap Module
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Create heatmap layers for positive, neutral and negative tweets
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // On success, 'data' contains a list of tweets.
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error: ' + err);
		        });
		}
		
		function addTweet(item) {
		    //Add tweet to the heat map arrays.
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio: " + str;
		    }
		}


**Изменение файла layout.cshtml**

1. В **обозревателе решений** последовательно разверните узлы **TweetSentimentWeb**, **Представления**, **Общий ресурс**, а затем дважды щелкните элемент \_**Layout.cshtml**.
2.  Замените содержимое на приведенное ниже:

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>



**Изменение файла Index.cshtml**

1. В **обозревателе решений** последовательно разверните узлы **TweetSentimentWeb**, **Представления**, **Главная**, а затем дважды щелкните элемент **Index.cshtml**.
2. Замените содержимое на приведенное ниже:

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**Изменение файла site.css**

1. В **обозревателе решений** последовательно разверните узлы **TweetSentimentWeb** и **Содержимое**, а затем дважды щелкните элемент **Site.css**.
2. Добавьте в файл следующий код:
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position: absolute;
		  top: 60px;
		  left: 75px;
		  z-index:1000;
		  font-size: 30px;
		}

**Изменение файла global.asax**

1. В **обозревателе решений** разверните узел **TweetSentimentWeb** и дважды щелкните элемент **Global.asax**.
2. Добавьте следующую инструкцию using:

        using System.Web.Http;

3. Добавьте в функцию **Application_Start()** следующие строки:

		// Register API routes
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	Modify the registration of the API routes to make Web API controller work inside of the MVC application.

**Запуск веб-приложения**

1. Убедитесь в том, что консольное приложение-служба потоковой передачи по-прежнему работает. Это позволит вам видеть изменения в режиме реального времени.
2. Нажмите клавишу **F5**, чтобы запустить веб-приложение.

    ![hdinsight.hbase.twitter.sentiment.bing.map][hdinsight.hbase.twitter.sentiment.bing.map]
3. Введите в текстовом поле ключевое слово и нажмите кнопку **Go** (Найти). В зависимости от того, какие данные собраны в таблице HBase, некоторые ключевые слова, возможно, не будут найдены. Попробуйте использовать распространенные ключевые слова, такие как "нравится", "xbox", "playstation" и т. д.
4. Переключитесь между категориями **Positive** (Положительное), **Neutral** (Нейтральное) и **Negative** (Отрицательное), чтобы сравнить мнения.
5.  Дайте службе потоковой передачи поработать еще час, а затем выполните поиск по тому же ключевому слову и сравните результаты.


При желании вы можете также развернуть приложение на веб-сайте Azure. Инструкции см. в разделе [Начало работы с Azure и ASP.NET][Начало работы с Azure и ASP.NET].

## <a id="nextsteps"></a> Дальнейшие действия

Из этого учебника вы узнали, как получать твиты, анализировать мнения на их основе, сохранять полученные оценки мнений в HBase и представлять данные по мнениям пользователей Twitter на картах Bing в режиме реального времени. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight][Приступая к работе с HDInsight]
- [Анализ данных Twitter с помощью Hadoop в HDInsight][Анализ данных Twitter с помощью Hadoop в HDInsight]
- [Анализ данных о задержке рейсов с помощью HDInsight][Анализ данных о задержке рейсов с помощью HDInsight]
- [Разработка программ потоковой передачи Hadoop на C# для HDInsight][Разработка программ потоковой передачи Hadoop на C# для HDInsight]
- [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]


[анализ мнений]: http://en.wikipedia.org/wiki/Sentiment_analysis
[]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[hdinsight.hbase.twitter.sentiment.bing.map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png
[]: https://github.com/maxluk/tweet-sentiment
[Предварительные требования]: #prerequisites
[Создание приложения Twitter]: #twitter
[Создание простой службы потоковой передачи данных Twitter]: #streaming
[Создание веб-сайта Azure для визуализации мнений пользователей Twitter]: #web
[Дальнейшие действия]: #nextsteps
[Приступая к использованию HBase с Hadoop в HDInsight]: ../hdinsight-hbase-get-started/
[Установка Visual Studio]: http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx
[OAuth]: http://oauth.net/
[1]: https://apps.twitter.com/
[hdi.hbase.twitter.sentiment.twitter.app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[Tweetinvi]: https://www.nuget.org/packages/TweetinviAPI/
[Protobuf-net]: https://www.nuget.org/packages/protobuf-net/
[2]: https://github.com/hdinsight/hbase-sdk-for-net
[hdinsight.hbase.twitter.sentiment.streaming.service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[3]: http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/
[Начало работы с Azure и ASP.NET]: ../web-sites-dotnet-get-started/
[Приступая к работе с HDInsight]: ../hdinsight-get-started/
[Анализ данных Twitter с помощью Hadoop в HDInsight]: ../hdinsight-analyze-twitter-data/
[Анализ данных о задержке рейсов с помощью HDInsight]: ../hdinsight-analyze-flight-delay-data/
[Разработка программ потоковой передачи Hadoop на C# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/

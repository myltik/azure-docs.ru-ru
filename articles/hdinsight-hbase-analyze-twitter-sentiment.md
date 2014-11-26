<properties urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight на платформе Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight

Узнайте, как проводить [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе данных большого размера с помощью HBase в кластере HDInsight (Hadoop).


Социальные веб-сайты являются одной из основных движущих сил для внедрения данных большого размера. Общедоступные API, предоставляемые сайтами, такими как Twitter, - полезный источник данных для анализа и понимания популярных тенденций. В этом учебнике вы создадите консольное приложение-службу, работающее в потоковом режиме, и веб-приложение ASP.NET для выполнения следующих задач:

![][img-app-arch]

- получение твитов с геотегами в режиме реального времени с помощью API потоковой передачи Twitter;
- оценка мнений на основе этих твитов;
- сохранение информации о мнениях в HBase с помощью пакета Microsoft HBase SDK;
- отображение статистических результатов в режиме реального времени на картах Bing с помощью веб-приложения ASP.NET. Визуальное представление твитов будет выглядеть примерно так:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	Вы сможете выполнять запросы, используя определенные ключевые слова, чтобы получить представление о том, является ли выражаемое в твитах мнение в целом положительным, отрицательным или нейтральным.

Пример готового решения Visual Studio можно найти на странице по адресу [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).






























##Содержание

- [Предварительные требования](#prerequisites)
- [Создание приложения Twitter](#twitter)
- [Создание простой службы потоковой передачи данных Twitter](#streaming)
- [Создание веб-сайта Azure для визуализации мнений пользователей Twitter](#web)
- [Дальнейшие действия](#nextsteps)

##<a id="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- **Кластер HBase в HDInsight**. Инструкции по подготовке кластера см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][hBase-get-started]. Для выполнения учебника необходимы следующие данные:

	<table border="1">
	<tr><th>Свойство кластера</th><th>Описание</th></tr>
	<tr><td>Имя кластера HBase,</td><td>Это имя вашего кластера HBase в HDInsight. Например: https://myhbase.azurehdinsight.net/</td></tr>
	<tr><td>Имя пользователя кластера</td><td>Имя учетной записи пользователя Hadoop. Имя пользователя Hadoop по умолчанию - <strong>admin</strong>.</td></tr>
	<tr><td>Пароль пользователя кластера</td><td>Пароль пользователя кластера Hadoop.</td></tr>
	</table>

- **Рабочая станция**, на которой установлено программное обеспечение Visual Studio 2013. Инструкции см. в разделе [Установка Visual Studio](http://msdn.microsoft.com/ru-ru/library/e2h7fzkw.aspx).





##<a id="twitter"></a>Создание идентификатора и секретов приложения Twitter

В интерфейсах API потоковой передачи Twitter для авторизации запросов используется протокол [OAuth](http://oauth.net/). 

Первый шаг для начала использования OAuth состоит в создании нового приложения на сайте разработчиков Twitter.

**Создание идентификатора и секретов приложения Twitter**

1. Войдите на веб-сайт [https://apps.twitter.com/](https://apps.twitter.com/). Нажмите кнопку **Войти сейчас**, если у вас нет учетной записи Twitter.
2. Щелкните **Создать новое приложение**.
3. Введите **имя**, **описание** и **веб-сайт**. Поле "Веб-сайт" на самом деле не используется. В нем не обязательно указывать действительный URL-адрес. В следующей таблице приведены некоторые примеры значений:

	<table border="1">
	<tr><th>Поле</th><th>Значение</th></tr>
	<tr><td>Название</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Описание</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Веб-сайт</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
	</table>

4. Установите значок **Да, я согласен**, и нажмите кнопку **Создать приложение Twitter**.
5. Щелкните вкладку **Разрешения**. Разрешение по умолчанию: **Только для чтения**. Этого разрешения достаточно для данного учебника. 
6. Щелкните вкладку **Ключи API**.
7. Нажмите кнопку **Создать маркер доступа**.
8. Нажмите кнопку **Проверить OAuth** в правом верхнем углу страницы.
9. Запишите значения параметров **API key** (Ключ API), **API secret** (Секрет API), **Access token** (Маркер доступа) и **Access token secret** (Секрет маркера доступа). Они потребуются позже в данном руководстве.

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> Создание простой службы потоковой передачи данных Twitter

Создайте консольное приложение для получения твитов, расчета оценки мнений на их основе и отправки обработанных слов из твитов в HBase.

**Создание  решения Visual Studio:**

1. Откройте **Visual Studio**.
2. В меню **File** (Файл) выберите пункт **New** (Создать), а затем щелкните **Project** (Проект).
3. Введите или выберите следующие значения:

	- Шаблоны: **Visual C#**
	- Шаблон: **Консольное приложение**
	- Имя: **TweetSentimentStreaming** 
	- Расположение: **C:\Tutorials**
	- Имя решения: **TweetSentimentStreaming**

4. Чтобы продолжить, нажмите кнопку **OK**.
 


**Установка пакетов Nuget и добавление ссылок SDK**

1. В меню **Tools** (Инструменты) щелкните **Nuget Package Manager** (Диспетчер пакетов NuGet), а затем **Package Manager Console** (Консоль диспетчера пакетов). В нижней части страницы откроется консольная панель.
2. Чтобы установить пакет [Tweetinvi](https://www.nuget.org/packages/TweetinviAPI/), используемый для доступа к API Twitter, и пакет [Protobuf-net](https://www.nuget.org/packages/protobuf-net/), который служит для сериализиции и десериализации объектов, выполните следующие команды:

		Install-Package TweetinviAPI (Установить-пакет TweetinviAPI)
		Install-Package protobuf-net (Установить-пакет TweetinviAPI) 

	> [WACOM.NOTE] На 26 августа 2014 г. пакет Microsoft Hbase SDK Nuget был не доступен. Репозиторий Github: [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Пока этот пакет SDK не станет доступен, выполнять сборку библиотеки DLL необходимо самостоятельно. Инструкции см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][hBase-get-started].

3. В **Обозревателе решений** щелкните правой кнопкой **Справочные материалы**, а затем нажмите **Добавить ссылку**.
4. В левой области разверните узел **Сборки**, а затем щелкните **Платформа**.
5. В правой области установите флажок перед пунктом **System.Configuration** и нажмите кнопку **OK**.



**Определение класса службы для потоковой передачи данных Twitter**

1. В **обозревателе решений** щелкните правой кнопкой мыши элемент **TweetSentimentStreaming**, наведите указатель на пункт **Добавить**, а затем щелкните **Класс**.
2. В поле **Имя** введите **HBaseWriter** и нажмите кнопку **Добавить**.
3. В начало файла **HBaseWriter.cs** добавьте следующие операторы:

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

        // Данные кластера HDinsight HBase и таблицы HBase
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // Для записи в HBase
        HBaseClient client;

        // словарь мнений для оценки мнений. Он загружается из отдельного файла.
        Dictionary<string, DictionaryItem> dictionary;
        
        // используется многопотоковая запись
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. Задайте значения таким константам, как **&lt;HBaseClusterName>**, **&lt;HadoopUserName>** и **&lt;HaddopUserPassword>**. Если вы хотите изменить имя таблицы HBase, вам необходимо соответствующим образом изменить имя таблицы в веб-приложении.

	Вы загрузите файл dictionary.tsv и переместите его в нужную папку позднее в этом учебнике.

7. Определите следующие функции внутри класса **HBaseWriter**:

		// Эта функция подключается к HBase, загружает словарь мнений и запускает поток для записи.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // создать таблицу HBase, если она не существует
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Загрузить словарь мнений из файла
            LoadDictionary();

			// Запустить поток для записи в HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Постановка полученных твитов в очередь
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Загрузить словарь мнений из файла
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

        // Расчет оценки мнения
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

		// Подготовка к записи широко используемого объекта CellSet в HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Разбиение твита на отдельные слова
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Расчет оценки мнения на основе слов
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // Добавление в таблицу HBase отдельной строки для каждого слова из твита
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Создание строки
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Добавление к строке следующих столбцов: идентификатор твита, язык, координатор (при наличии) и мнение 
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

        // Запись твита (CellSet) в HBase
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

                        // Запись твита в таблицу HBase в виде набора слов, распределенных по клеткам
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

	- **Подключение к Hbase [ HBaseWriter() ]**: Используйте пакет HBase SDK для создания объекта *ClusterCredentials* с URL-адресом кластера и учетными данными пользователя Hadoop, а затем создайте с его помощью объект *HBaseClient*.
	- **Создание таблицы HBase [ HBaseWriter() ]**: Используйте вызов метода *HBaseClient.CreateTable()*.
	- **Запись в таблицу HBase [ WriterThreadFunction() ]**: Используйте вызов метода *HBaseClient.StoreCells()*.

**Подготовка файла Program.cs**

1. В **обозревателе решений** дважды щелкните **Program.cs**, чтобы открыть этот файл.
2. Добавьте в начало файла следующие операторы using:

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. В классе **Program**  определите следующие константы:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. Установите значения констант в соответствии со значениями в приложении Twitter.

3. Измените функцию **Main()** так, чтобы она выглядела следующим образом:

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. Добавьте в класс следующую функцию:

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

                        // Запись твита в HBase
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

1. Перейдите на страницу [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).
2. Нажмите кнопку **Download ZIP** (Загрузить ZIP-файл).
3. Распакуйте архив на локальном компьютере.
4. Скопируйте файл **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5. Вставьте этот файл в решение по пути **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv**.

**Запуск службы потоковой передачи**

1. В Visual Studio нажмите клавишу **F5**. Вот снимок экрана с консольным приложением:

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Оставьте консольное приложение потоковой передачи запущенным, пока вы занимаетесь разработкой веб-приложения. Это даст возможность собрать больше данных.



























##<a id="web"></a> Создание веб-сайта Azure для визуализации мнений пользователей Twitter

Этот подраздел посвящен созданию веб-приложения ASP.NET MVC, предназначенному для чтения мнений из HBase в режиме реального времени и отображения данных на картах Bing.

**Создание веб-приложения ASP.NET MVC**

1. Откройте Visual Studio.
2. Щелкните **Файл**, потом **Создать**, а затем **Проект**.
3. Введите следующие данные:

	- Категория шаблона: **Visual C#/Веб-сайт**
	- Шаблон: **Веб-приложение ASP.NET**
	- Имя: **TweetSentimentWeb**
	- Расположение: **C:\Tutorials** 
4. Нажмите кнопку **OK**.
5. В разделе **Выбор шаблона** щелкните **MVC**. 
6. В **Microsoft Azure** щелкните **Manage Subscriptions** (Управление подписками).
7. В окне **Manage Microsoft Azure Subscriptions** (Управление подписками Microsoft Azure) щелкните **Sign in** (Вход).
8. Введите свои учетные данные для Azure. Сведения о вашей подписке Azure появятся на вкладке Accounts (Учетные записи).
9. Нажмите кнопку **Close** (Закрыть), чтобы закрыть окно Manage Microsoft Azure Subscriptions.
10. В окне **New ASP.NET Project - TweetSentimentWeb** (Новый проект ASP.NET - TweetSentimentWeb) нажмите кнопку **OK**.   
11. В окне **Configure Microsoft Azure Site Settings** (Настройка параметров сайта Microsoft Azure) выберите ближайший к вам регион, установив параметр **Region**. Указывать сервер базы данных не нужно. 
12. Нажмите кнопку **OK**.

**Установка пакетов Nuget**

1. В меню **Tools** (Инструменты) щелкните **Nuget Package Manager** (Диспетчер пакетов NuGet), а затем **Package Manager Console** (Консоль диспетчера пакетов). В нижней части страницы откроется консольная панель.
2. Чтобы установить пакет [Protobuf-net](https://www.nuget.org/packages/protobuf-net/), используемый для сериализиции и десериализации объектов, используйте следующую команду:

		Install-Package protobuf-net (Установить-пакет TweetinviAPI) 

	> [WACOM.NOTE] На 20 августа 2014 г. пакет Microsoft Hbase SDK Nuget был не доступен. Репозиторий Github: [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Пока этот пакет SDK не станет доступен, выполнять сборку библиотеки DLL необходимо самостоятельно. Инструкции см. в разделе [Приступая к использованию HBase с Hadoop в HDInsight][hBase-get-started].

**Добавление класса HBaseReader**

1. В **обозревателе решений** разверните элемент **TweetSentiment**.
2. Щелкните правой кнопкой мыши **Модели**, после этого щелкните **Добавить**, а затем щелкните **Класс**.
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
		        // Для чтения мнений, находящихся в твитах, из HDInsight HBase
		        HBaseClient client;
		
		        // Данные кластера HDinsight HBase и таблицы HBase
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // Конструктор
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Асинхронная отправка в очередь мнений, выраженных в твитах, из таблицы HBase 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Демонстрация процедуры фильтрации данных, накопленных за последние 6 часов в ключе строки
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
		
		            // Выполнение асинхронного вызова для выполнения сканирования
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // поиск ячейки, содержащей комбинацию символов "d:coor" 
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

4. В классе **HBaseReader** измените значения констант:

	- **CLUSTERNAME**: имя кластера HBase, например, *https://<HBaseClusterName>.azurehdinsight.net/*. 
    - **HADOOPUSERNAME**: имя пользователя Hadoop в кластере HBase. Имя по умолчанию - *admin*.
    - **HADOOPUSERPASSWORD**: пароль пользователя Hadoop в кластере HBase.
    - **HBASETABLENAME** = "tweets_by_words";

	Имя таблицы HBase - "tweets_by_words". Значения должны совпадать со значениями, отправленными в службу потоковой передачи, чтобы веб-приложение считывало данные из той же таблицы HBase.




**Добавление контроллера TweetsController**

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**.
2. Щелкните правой кнопкой мыши **Контроллеры**, потом щелкните **Добавить**, а затем **Контроллер**.
3. Щелкните элемент **Web API 2 Controller - Empty** (Контроллер веб-интерфейса API 2 - пустой) и нажмите кнопку **Добавить**.
4. В поле имени контроллера введите **TweetsController** и нажмите кнопку **Добавить**.
5. В **обозревателе решений** дважды щелкните файл TweetsController.cs, чтобы открыть его.
5. Измените файл так, чтобы он выглядел следующим образом:

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
2. Щелкните правой кнопкой мыши **Скрипты**, затем щелкните **Добавить**, а после этого **Файл JavaScript**.
3. В поле имени элемента введите **heatmap.js**.
4. Скопируйте приведенный ниже код и вставьте его в файл. Код был создан Аластером Эйтчисоном (Alastair Aitchison). Дополнительные сведения см. на странице [http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/).

		/*******************************************************************************
		* Автор: Аластер Эйтчисон (Alastair Aitchison)
		* Веб-сайт: http://alastaira.wordpress.com
		* Дата: 15 апреля 2011 г.
		* 
		* Описание: 
		* В данном файле JavaScript представлен алгоритм, который можно использовать для добавления графического элемента, отображающего тепловую карту,
		* на компонент управления Bing Maps версии 7. Предусматривается простая настройка палитры,
		* отображающей на тепловой карте интенсивность и температуру.
		*
		* Требования:
		* Слой тепловой карты создается динамически на стороне клиента с помощью
		* HTML5-элемента <canvas> (холст), и, следовательно, для этого требуется браузер, поддерживающий
		* отображение этого элемента. Данный вариант был проверен на браузерах IE9, Firefox 3.6/4 и 
		* Chrome 10. Я был бы крайне признателен пользователям за информацию относительно того, 
		* работает ли данный код на других браузерах или нет.
		
		* Использование:
		* Для конструктора HeatMapLayer требуются:
		* - Ссылка на объект карты
		* - Массив или элементы Microsoft.Maps.Location
		* - Дополнительные параметры для настройки визуального представления слоя
		*  (радиус, единица измерения, интенсивность и цветовой градиент (ColourGradient)), а также функция обратного вызова
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Частные свойства  */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Задание параметров по умолчанию
		    var _options = {
		        // Прозрачность в центре каждой точки тепловой карты
		        intensity: 0.5,
		
		        // Затронутый радиус каждой точки тепловой карты
		        radius: 1000,
		
		        // Является ли радиус абсолютной величиной, выражаемой в пикселях, или метрической единицей
		        unit: 'meters',
		
		        // Цветовой температурный градиент карты
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Пурпурный
		            "0.25": 'rgba(0,0,255,40)',    // Синий
		            "0.50": 'rgba(0,255,0,80)',    // Зеленый
		            "0.75": 'rgba(255,255,0,120)', // Желтый
		            "1.00": 'rgba(255,0,0,150)'    // Красный
		        },
		
		        // Функция обратного вызова, которая срабатывает после перерисовки слоя тепловой карты 
		        callback: null
		    };
		
		    /* Закрытые методы */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Создание элемента "canvas" (холст)
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Замена величин, заданных по умолчанию, на значения, передаваемые конструктором
		            _setOptions(options);
		
		            // Загрузка массива с данными о координатах
		            _setPoints(locations);
		
		            // Создание цветового градиента на основе предоставленных цветовых точек
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Привязка обработчика событий к процедуре перерисовки холста (canvas) с тепловой картой
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Сброс настроек тепловой карты
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Создание цветового градиента на основе предоставленных цветовых точек при инициализации
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
		
		    // Применение цветового градиента к карте интенсивности
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix является массивом CanvasPixelArray, в котором содержатся данные объемом, определяемым по формуле: высота x ширина x 4 байта (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // получение значения для альфа-канала данного пикселя
		            if (a != 0) { // Если имеются данные для отображения
		                pix[p] = _temperaturemap[a]; // задание значения красного цветового оттенка из градиента, соответствующего данному альфа-каналу
		                pix[p + 1] = _temperaturemap[a + 1]; //задание значения для зеленого цвета на основе альфа-канала
		                pix[p + 2] = _temperaturemap[a + 2]; //задание значения для синего цвета на основе альфа-канала
		            }
		            p += 4; // Перейти к следующему пикселю
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Задание входных параметров
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Задание точек тепловой карты из массива Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Главный метод для перерисовки тепловой карты
		    function _createHeatMap() {
		        // Проверка соответствия размеров холста и карты
		        // Это влияет на изменение параметров холста
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Расчет радиуса пикселя каждой точки тепловой карты для заданной величины масштаба выбранной карты
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Преобразование значений широты и долготы в координаты пикселя
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Создание карты интенсивности на основе циклического прохода по каждой координате
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Создание радиального градиента с центом в данной точке
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Рисование точки тепловой карты на холсте
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Применение выбранного цветового градиента к карте интенсивности
		        _colouriseHeatMap();
		
		        // Вызов функции обратного вызова, если это определено
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Открытые методы */
		
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
		
		    // Задание параметров для интенсивности, радиуса, цветового градиента и т. д.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Задание массива из Microsoft.Maps.Locations, на основе которого создается тепловая карта
		    this.SetPoints = function (locations) {
		        // Сброс настроек существующего слоя тепловой карты
		        _clearHeatMap();
		        // Передача нового набора координат
		        _setPoints(locations);
		        // Повторное создание слоя
		        _createHeatMap();
		    }
		
		    // Удаление слоя тепловой карты из DOM
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
		
		    // Вызов процедуры инициализации
		    _init();
		};
		
		// Вызов метода загруженного модуля
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**Добавление файла tweetStream.js**

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**.
2. Щелкните правой кнопкой мыши **Скрипты**, затем щелкните **Добавить**, а после этого **Файл JavaScript**.
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
		    // Инициализация карты
		    var options = {
		        credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2,5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Параметры тепловой карты для слоев с положительными, нейтральными и отрицательными значениями
		
		    var heatmapOptions = {
		        // Прозрачность в центре каждой точки тепловой карты
		        intensity: 0.5,
		
		        // Затронутый радиус каждой точки тепловой карты
		        radius: 15,
		
		        // Является ли радиус абсолютной величиной, выражаемой в пикселях, или метрической единицей
		        unit: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Прозрачность в центре каждой точки тепловой карты
		        intensity: 0.5,
		
		        // Затронутый радиус каждой точки тепловой карты
		        radius: 15,
		
		        // Является ли радиус абсолютной величиной, выражаемой в пикселях, или метрической единицей
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
		        // Прозрачность в центре каждой точки тепловой карты
		        intensity: 0.5,
		
		        // Затронутый радиус каждой точки тепловой карты
		        radius: 15,
		
		        // Является ли радиус абсолютной величиной, выражаемой в пикселях, или метрической единицей
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
		
		    // Регистрация и загрузка клиентского модуля тепловой карты
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Создание уровней тепловой карты для положительных, нейтральных и отрицательных твитов
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
		
		            // При успешном исходе, в 'data' содержится список твитов.
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
		    //Добавление твита к массиву тепловой карты.
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

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**, затем разверните **Представления**, потом **Общие**, а после этого щелкните два раза по _**Layout.cshtml**.
2. Замените содержимое на приведенное ниже:

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

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**, затем разверните **Представления**, потом **Общие**, а после этого щелкните два раза по **Index.cshtml**.
2. Замените содержимое на приведенное ниже:

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**Изменение файла site.css**

1. В **обозревателе решений**, разверните элемент **TweetSentimentWeb**, потом **Содержание**, а затем два раза щелкните **Site.css**.
2. Добавьте в файл следующий код:
		
		/* создание контейнера, а следовательно и карты, шириной 100 % */
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

1. В **обозревателе решений** разверните элемент **TweetSentimentWeb**, а затем дважды щелкните **Global.asax**.
2. Добавьте следующую инструкцию using:

		using System.Web.Http;

2. Добавьте в функцию **Application_Start()** следующие строки:

		// Регистрация маршрутов API
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	Измените регистрацию маршрутов API так, чтобы веб-контроллер API работал внутри приложения MVC.

**Запуск веб-приложения**

1. Убедитесь в том, что консольное приложение-служба потоковой передачи по-прежнему работает. Это позволит вам видеть изменения в режиме реального времени.
2. Нажмите клавишу **F5**, чтобы запустить веб-приложение:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. Введите в текстовом поле ключевое слово и нажмите кнопку **Go** (Найти).  В зависимости от того, какие данные собраны в таблице HBase, некоторые ключевые слова, возможно, не будут найдены. Попробуйте использовать распространенные ключевые слова, такие как "нравится", "xbox", "playstation" и т. д. 
3. Переключайтесь между **Положительными**, **Нейтральными** и **Отрицательными** значениями, чтобы сравнить мнения по выбранной теме.
4. Дайте службе потоковой передачи поработать еще час, а затем выполните поиск по тому же ключевому слову и сравните результаты.

 
При желании вы можете также развернуть приложение на веб-сайте Azure.  Инструкции см. в разделе [Начало работы с веб-сайтами Azure и ASP.NET][website-get-started].
 
##<a id="nextsteps"></a>Дальнейшие действия

Из этого учебника вы узнали, как получать твиты, анализировать мнения на их основе, сохранять полученные оценки мнений в HBase и представлять данные по мнениям пользователей Twitter на картах Bing в режиме реального времени. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Анализ данных Twitter с помощью Hadoop в HDInsight][hdinsight-analyze-twitter-data]
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data]
- [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started/
[website-get-started]: ../web-sites-dotnet-get-started/



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-analyze-twitter-data]: ../hdinsight-analyze-twitter-data/
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started/




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/


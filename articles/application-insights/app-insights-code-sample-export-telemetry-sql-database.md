<properties 
	pageTitle="Пример кода. Экспорт в SQL из Application Insights с использованием рабочей роли" 
	description="Напишите код анализа телеметрии в Application Insights с помощью функции непрерывного экспорта." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="klin"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="awills"/>
 
# Пример кода. Экспорт в SQL из Application Insights с использованием рабочей роли

В этой статье показано, как переместить данные телеметрии из [Visual Studio Application Insights][start] в базу данных SQL Azure, используя [непрерывный экспорт][export] и обходясь без написания длинного кода.

Непрерывный экспорт перемещает данные телеметрии в формате JSON в службу хранилища Azure, поэтому мы напишем код для анализа объектов JSON и создадим строки в таблице базы данных.

(Вообще, непрерывный экспорт представляет собой способ для самостоятельного анализа телеметрии, которую приложения отправляют в Application Insights. Этот пример кода можно адаптировать для выполнения других задач с экспортированной телеметрией.)

Для начала предположим, что у вас уже есть приложение, мониторинг которого нужно выполнить.

## Добавление пакета SDK для Application Insights

Чтобы выполнить мониторинг приложения, [добавьте пакет SDK для Application Insights][start] в приложение. Для различных платформ, IDE и языков разработано множество различных пакетов SDK и вспомогательных инструментов. Вы можете выполнять мониторинг веб-страниц, веб-серверов Java или ASP.NET и мобильных устройств нескольких типов. Все пакеты SDK отправляют данные телеметрии на [портал Application Insights][portal], где вы можете использовать эффективные инструменты анализа и диагностики, а также экспортировать данные в хранилище.

Чтобы начать работу:

1. Получите [учетную запись в Microsoft Azure](http://azure.microsoft.com/pricing/).
2. На [портале Azure][portal] добавьте новый ресурс Application Insights для своего приложения:

    ![Выберите «Создать», «Службы для разработчиков», Application Insights и тип приложения.](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    (Тип вашего приложения и подписка могут отличаться.)
3. Откройте «Быстрый запуск», чтобы узнать, как установить пакет SDK для приложения необходимого типа.

    ![Выберите «Быстрый запуск» и следуйте указаниям.](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    Если в списке отсутствует необходимый тип приложения, просмотрите страницу [Начало работы][start].

4. В этом примере мы выполним мониторинг веб-приложения, поэтому будем использовать инструменты Azure в Visual Studio для установки пакета SDK. Зададим имя ресурса Application Insights:

    ![В Visual Studio в диалоговом окне «Создать проект» установите флажок «Добавить Application Insights» и в поле «Отправка телеметрии» выберите создание приложения или использование существующего.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Создание хранилища в Azure

1. Создайте учетную запись хранения в подписке на [портале Azure][portal].

    ![На портале Azure выберите «Создать», «Данные», «Хранилище».](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. Создание контейнера

    ![В новом хранилище выберите «Контейнеры», а затем «Добавить».](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Запуск непрерывного экспорта в службе хранилища Azure

1. На портале Azure перейдите к ресурсу Application Insights, созданному для приложения.

    ![Выберите «Обзор», Application Insights, а затем выберите свое приложение.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. Создайте непрерывный экспорт.

    ![Выберите «Параметры», «Непрерывный экспорт», «Добавить».](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    Выберите учетную запись хранения, созданную ранее:

    ![Установите место назначения экспорта.](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    Задайте необходимые типы событий:

    ![Выберите типы событий.](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

После завершения предоставьте пользователям возможность попользоваться приложением на протяжении некоторого времени. После получения данных телеметрии в [обозревателе метрик][metrics] отобразятся статистические диаграммы, а в разделе [Поиск по журналу диагностики][diagnostic] – отдельные события.

Кроме того, данные будут экспортированы в хранилище, в котором можно просмотреть содержимое. Например, в Visual Studio есть обозреватель хранилища:


![В Visual Studio откройте «Обозреватель сервера», «Azure», «Хранилище».](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

События записываются в JSON-файлы больших двоичных объектов. В каждом файле может содержаться одно или несколько событий. Поэтому мы напишем код для чтения данных событий и фильтрации необходимых полей. Вообще с данными можно выполнять любые действия, но сейчас мы напишем код для перемещения данных в базу данных SQL. Это облегчит выполнение многих любопытных запросов.

## Создание базы данных SQL Azure

И снова начнем с использованием вашей подписки на [портале Azure][portal]. Создайте базу данных (и новый сервер, если его еще нет), куда будут записываться данные.

![«Создать», «Данные», SQL.](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


Убедитесь, что на сервере базы данных разрешен доступ к службам Azure:


![«Обзор», «Серверы», ваш сервер, «Параметры», «Брандмауэр», «Разрешить доступ к Azure».](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## Создание рабочей роли 

Теперь можно написать [код](https://sesitai.codeplex.com/) для анализа JSON-данных в экспортированных больших двоичных объектах и создать записи в базе данных. Так как хранилище для экспорта и база данных находятся в Azure, мы выполним код, используя рабочую роль Azure.


#### Создание проекта рабочей роли

В Visual Studio создайте новый проект для рабочей роли:

![«Создать проект», «Visual C#», «Облако», «Облачная служба Azure».](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![В диалогом окне новой облачной службы выберите «Visual C#» и «Рабочая роль».](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### Подключение к учетной записи хранения

В Azure найдите строку подключения в своей учетной записи хранения:

![В учетной записи хранения выберите «Ключи» и скопируйте основную строку подключения.](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

В Visual Studio настройте параметры рабочей роли, используя строку подключения учетной записи хранения:


![В обозревателе решений в проекте облачной службы разверните «Роли» и откройте рабочую роль. Откройте вкладку параметров, выберите «Добавить параметр» и задайте имя StorageConnectionString и строку подключения в качестве типа, а затем щелкните, чтобы задать значение. Задайте его вручную и вставьте строку подключения.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### Пакеты

В обозревателе решений щелкните правой кнопкой мыши свой проект рабочей роли и выберите «Управление пакетами NuGet». Найдите и установите такие пакеты:

 * EntityFramework 6.1.2 или более поздней версии – мы используем его, чтобы создать схему таблицы базы данных в режиме реального времени на основе содержимого JSON-документа в большом двоичном объекте;
 * JsonFx – мы используем его, чтобы преобразовать JSON-данные в свойства класса C#.

Используйте этот инструмент, чтобы создать класс C# из одного JSON-документа. Для этого необходимо внести некоторые незначительные изменения, например преобразовать JSON-массивы в единое свойство C# и один столбец в таблице базы данных (например, urlData_port).

 * [Генератор классов C# из JSON-документов](http://jsonclassgenerator.codeplex.com/).

## Код 

Этот код можно вставить в файл `WorkerRole.cs`.

#### Импорт

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### Получение строки подключения к хранилищу

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### Выполнение рабочей роли с регулярными интервалами

Замените существующий метод выполнения и выберите нужный интервал. Интервал должен иметь длительность не меньше одного часа, так как функция экспорта экспортирует один объект JSON за час.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000
                
        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### Вставка каждого объекта JSON в качестве строки таблицы


    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
       	  {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
		//handle exception
      }
    }

#### Анализ каждого большого двоичного объекта

    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());
    
        string json;
    
        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    
          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
    
          recCount = entities.Count();
          failureCount = 0; //resetting failure count
    
          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);
    
            Dictionary<string, object> dict = new Dictionary<string, object>();
    
            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");
    
            switch (FilterType)
            {
              case "PageViewPerformance":
    
              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }
    
            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);
    
            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;
    
            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### Подготовка словаря для каждого JSON-документа


    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }
        }

#### Преобразование JSON-документа в свойства объекта телеметрии класса C#

     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }

            return res;
        }

#### Файл класса PageViewPerformance, созданный из JSON-документа

    public class PageViewPerformance
    {
    	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### DBcontext для взаимодействия с SQL с помощью Entity Framework

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Добавьте строку подключения базы данных с именем `TelemetryContext` в `app.config`.

## Схема (только для информирования)

Это схема для таблицы, которая будет создана для PageView.

> [AZURE.NOTE]Вам не нужно выполнять этот сценарий. Атрибуты в JSON-документе определяют столбцы в таблице.

    CREATE TABLE [dbo].[PageViewPerformances](
	[Id] [uniqueidentifier] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlData_port] [int] NOT NULL,
	[urlData_protocol] [nvarchar](max) NULL,
	[urlData_host] [nvarchar](max) NULL,
	[urlData_base] [nvarchar](max) NULL,
	[urlData_hashTag] [nvarchar](max) NULL,
	[total_value] [float] NOT NULL,
	[networkConnection_value] [float] NOT NULL,
	[sendRequest_value] [float] NOT NULL,
	[receiveRequest_value] [float] NOT NULL,
	[clientProcess_value] [float] NOT NULL,
	[name] [nvarchar](max) NULL,
	[User] [nvarchar](max) NULL,
	[internal_data_id] [nvarchar](max) NULL,
	[internal_data_documentVersion] [nvarchar](max) NULL,
	[context_data_eventTime] [datetime] NULL,
	[context_device_id] [nvarchar](max) NULL,
	[context_device_type] [nvarchar](max) NULL,
	[context_device_os] [nvarchar](max) NULL,
	[context_device_osVersion] [nvarchar](max) NULL,
	[context_device_locale] [nvarchar](max) NULL,
	[context_device_userAgent] [nvarchar](max) NULL,
	[context_device_browser] [nvarchar](max) NULL,
	[context_device_browserVersion] [nvarchar](max) NULL,
	[context_device_screenResolution_value] [nvarchar](max) NULL,
	[context_user_anonId] [nvarchar](max) NULL,
	[context_user_anonAcquisitionDate] [nvarchar](max) NULL,
	[context_user_authAcquisitionDate] [nvarchar](max) NULL,
	[context_user_accountAcquisitionDate] [nvarchar](max) NULL,
	[context_session_id] [nvarchar](max) NULL,
	[context_session_isFirst] [bit] NOT NULL,
	[context_operation_id] [nvarchar](max) NULL,
	[context_location_point_lat] [float] NOT NULL,
	[context_location_point_lon] [float] NOT NULL,
	[context_location_clientip] [nvarchar](max) NULL,
	[context_location_continent] [nvarchar](max) NULL,
	[context_location_country] [nvarchar](max) NULL,
	[context_location_province] [nvarchar](max) NULL,
	[context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Чтобы увидеть этот пример в действии, [загрузите](https://sesitai.codeplex.com/) полный рабочий код, измените параметры `app.config` и опубликуйте рабочую роль в Azure.


## Связанные статьи

* [Экспорт в SQL с использованием рабочей роли](app-insights-code-sample-export-telemetry-sql-database.md)
* [Непрерывный экспорт в Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->
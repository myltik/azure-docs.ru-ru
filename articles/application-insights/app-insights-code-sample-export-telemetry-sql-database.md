---
title: "Пример кода: анализ данных, экспортированных из Application Insights| Документация Майкрософт"
description: "Напишите код анализа телеметрии в Application Insights с помощью функции непрерывного экспорта. Сохраните данные в SQL."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: b0782ed5675e5256694f7b9f4e98750e57d23e0a


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>Пример кода: анализ данных, экспортированных из Application Insights
В этой статье показано, как создать код для обработки данных, экспортированных из [Azure Application Insights][start] с помощью [непрерывного экспорта][export]. Непрерывный экспорт перемещает данные телеметрии в формате JSON в службу хранилища Azure, поэтому мы напишем код для анализа объектов JSON и создадим строки в таблице базы данных.

В качестве примера мы напишем код для перемещения данных телеметрии из Application Insights в базу данных SQL Azure.

Прежде чем начать, обратите внимание на следующее.

* Более эффективный способ передачи экспортированных данных в базу данных — [с помощью Stream Analytics](app-insights-code-sample-export-sql-stream-analytics.md), но сейчас нашей целью является показать вам пример кода для обработки экспортированных данных. Этот пример кода можно адаптировать для выполнения других задач с экспортированными данными телеметрии.
* В этом примере мы перемещаем данные в базу данных Azure, выполняя код в рабочей роли Azure. Но вы можете адаптировать этот код для запуска на локальном сервере, чтобы передавать данные на локальный сервер SQL Server.
* Вы можете [написать код для прямого доступа к данным телеметрии](http://dev.applicationinsights.io/) в Application Insights без их экспорта.

Если вы еще не начали отслеживать веб-приложение с помощью Application Insights, [сделать это сейчас][start].



## <a name="create-storage-in-azure"></a>Создание хранилища в Azure
Данные из Application Insights всегда экспортируются в учетную запись хранения Azure в формате JSON. Это именно то хранилище, из которого ваш код будет считывать данные.

1. Создайте "классическую" учетную запись хранения в подписке на [портале Azure][portal].
   
    ![На портале Azure выберите "Создать", "Данные", "Хранилище".](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. Создание контейнера
   
    ![В новом хранилище выберите «Контейнеры», щелкните элемент «Контейнеры», а затем — «Добавить».](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>Запуск непрерывного экспорта в службе хранилища Azure
1. На портале Azure перейдите к ресурсу Application Insights, созданному для приложения.
   
    ![Выберите "Обзор", Application Insights, а затем выберите свое приложение.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. Создайте непрерывный экспорт.
   
    ![Выберите "Параметры", "Непрерывный экспорт", "Добавить".](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    Выберите учетную запись хранения, созданную ранее:

    ![Установите место назначения экспорта.](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    Задайте необходимые типы событий:

    ![Выберите типы событий.](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. Пусть данные накопятся. Предоставьте пользователям возможность поработать с приложением на протяжении некоторого времени. После получения данных телеметрии в [обозревателе метрик](app-insights-metrics-explorer.md) отобразятся статистические диаграммы, а в разделе [поиска по журналу диагностики](app-insights-diagnostic-search.md) — отдельные события. 
   
    Данные также будут экспортированы в хранилище. 
2. Проверьте экспортированные данные. В Visual Studio откройте меню **"Вид" или "Обозреватель облака"**и выберите элемент "Azure" или "Хранилище". (Если этой команды нет в меню, установите пакет SDK Azure: откройте диалоговое окно «Создание проекта», разверните узел «Visual C#/облако» и выберите «Получить Microsoft Azure SDK для .NET».)
   
    ![В Visual Studio откройте "Обозреватель сервера", "Azure", "Хранилище".](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    Запишите общую часть имени пути, которое образовано от имени приложения и ключа инструментирования. 

События записываются в JSON-файлы больших двоичных объектов. Каждый файл может содержать одно или несколько событий. Поэтому нам нужна возможность считывать данные событий и отфильтровывать необходимые поля. Вообще с данными можно выполнять любые действия, но сейчас мы напишем код для перемещения данных в базу данных SQL. Это облегчит выполнение многих любопытных запросов.

## <a name="create-an-azure-sql-database"></a>Создание базы данных SQL Azure
В этом примере мы напишем код для передачи данных в базу данных.

В своей подписке на [портале Azure][portal] создайте базу данных (и сервер, если у вас его еще нет), куда будут записываться данные.

!["Создать", "Данные", SQL.](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

Убедитесь, что на сервере базы данных разрешен доступ к службам Azure:

![«Обзор», «Серверы», ваш сервер, «Параметры», «Брандмауэр», «Разрешить доступ к Azure».](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>Создание рабочей роли
Теперь можно написать [код](https://sesitai.codeplex.com/) для анализа данных JSON в экспортированных больших двоичных объектах и создания записей в базе данных. Так как хранилище для экспорта и база данных находятся в Azure, мы выполним код, используя рабочую роль Azure.

Этот код автоматически извлекает любые свойства, имеющиеся в JSON. Описание этих свойств см. в статье [Экспорт модели данных Application Insights](app-insights-export-data-model.md).

#### <a name="create-worker-role-project"></a>Создание проекта рабочей роли
В Visual Studio создайте новый проект для рабочей роли:

![«Создать проект», «Visual C#», «Облако», «Облачная служба Azure».](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![В диалогом окне новой облачной службы выберите «Visual C#» и «Рабочая роль».](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>Подключение к учетной записи хранения
В Azure найдите строку подключения в своей учетной записи хранения:

![В учетной записи хранения выберите «Ключи» и скопируйте основную строку подключения.](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

В Visual Studio настройте параметры рабочей роли, используя строку подключения учетной записи хранения:

![В обозревателе решений в проекте облачной службы разверните «Роли» и откройте рабочую роль. Откройте вкладку параметров, выберите «Добавить параметр» и задайте имя StorageConnectionString и строку подключения в качестве типа, а затем щелкните, чтобы задать значение. Задайте его вручную и вставьте строку подключения.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>Пакеты
В обозревателе решений щелкните правой кнопкой мыши свой проект рабочей роли и выберите «Управление пакетами NuGet».
Найдите и установите такие пакеты: 

* EntityFramework 6.1.2 или более поздней версии – мы используем его, чтобы создать схему таблицы базы данных в режиме реального времени на основе содержимого JSON-документа в большом двоичном объекте;
* JsonFx — мы используем его, чтобы преобразовать данные JSON в свойства класса C#.

Используйте этот инструмент, чтобы создать класс C# из одного JSON-документа. Для этого необходимо внести некоторые незначительные изменения, например преобразовать массивы JSON в единое свойство C# и один столбец в таблице базы данных (например, urlData_port). 

* [Генератор классов C# из JSON-документов.](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>Код
Этот код можно вставить в файл `WorkerRole.cs`.

#### <a name="imports"></a>Импорт
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>Получение строки подключения к хранилищу
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>Выполнение рабочей роли с регулярными интервалами
Замените существующий метод выполнения и выберите нужный интервал. Интервал должен иметь  длительность не меньше одного часа, так как функция экспорта экспортирует один объект JSON за час.

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

#### <a name="insert-each-json-object-as-a-table-row"></a>Вставка каждого объекта JSON в качестве строки таблицы
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

#### <a name="parse-each-blob"></a>Анализ каждого большого двоичного объекта
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

#### <a name="prepare-a-dictionary-for-each-json-document"></a>Подготовка словаря для каждого JSON-документа
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

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>Преобразование JSON-документа в свойства объекта телеметрии класса C#
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

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>Файл класса PageViewPerformance, созданный из JSON-документа
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


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>DBcontext для взаимодействия с SQL с помощью Entity Framework
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Добавьте строку подключения базы данных с именем `TelemetryContext` в `app.config`.

## <a name="schema-information-only"></a>Схема (только для информирования)
Это схема для таблицы, которая будет создана для PageView.

> [!NOTE]
> Вам не нужно выполнять этот сценарий. Атрибуты в JSON-документе определяют столбцы в таблице.
> 
> 

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


Чтобы увидеть этот пример в действии, [скачайте](https://sesitai.codeplex.com/) полный рабочий код, измените параметры `app.config` и опубликуйте рабочую роль в Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание кода для прямого доступа к данным телеметрии](http://dev.applicationinsights.io/)
* [Экспорт в SQL с использованием рабочей роли](app-insights-code-sample-export-telemetry-sql-database.md)
* [Непрерывный экспорт в Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [Экспорт модели данных](app-insights-export-data-model.md)
* [Дополнительные примеры и пошаговые руководства](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Nov16_HO3-->



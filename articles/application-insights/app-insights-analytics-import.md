---
title: Импорт данных в инструмент аналитики в Azure Application Insights | Документация Майкрософт
description: Импорт статических данных для объединения с данными телеметрии приложения или импорт отдельного потока данных для выполнения запроса с помощью аналитики.
services: application-insights
keywords: открытие схемы, импорт данных
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 688d620e19a8a6f536d134d9c4d7c837ec06bbdc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293627"
---
# <a name="import-data-into-analytics"></a>Импорт данных в инструмент аналитики

Импортируйте табличные данные в инструмент [аналитики](app-insights-analytics.md), чтобы объединить их с данными телеметрии [Application Insights](app-insights-overview.md) из приложения или проанализировать в отдельном потоке. Аналитика — это эффективный язык запросов, который удобно использовать для анализа больших потоков данных телеметрии с метками времени.

Вы можете импортировать данные в инструмент аналитики, используя собственную схему. В этой схеме не обязательно использовать стандартные схемы Application Insights, например запрос или трассировку.

Можно импортировать файлы в формате JSON или DSV (значения с разделителями — запятыми, точками с запятой или знаками табуляции).

Импорт в инструмент аналитики может понадобиться в трех ситуациях.

* **Объединение с данными телеметрии приложения.** Например, вы можете импортировать таблицу, которая сопоставляет URL-адреса веб-сайта с удобными для чтения заголовками страниц. В инструменте аналитики можно создать отчет-диаграмму панели мониторинга, показывающий десять наиболее популярных страниц веб-сайта. Теперь вместо URL-адресов он может отображать заголовки страниц.
* **Сопоставление данных телеметрии приложения** с другими источниками, например с отчетом о сетевом трафике, данными сервера или файлами журнала CDN.
* **Применение аналитики в отдельном потоке данных.** Аналитика Application Insights — мощный инструмент, который хорошо работает с разреженным потоками с метками времени. Во многих случаях этот инструмент намного эффективнее, чем SQL. С помощью аналитики вы можете проанализировать такой поток из другого источника.

Отправлять данные в источник данных очень легко. 

1. (Однократно.) Определите схему данных в источнике данных.
2. (Периодически.) Передайте данные в службу хранилища Azure и вызовите REST API, чтобы получать уведомления о новых данных, ожидающих приема. Через несколько минут данные будут доступны для запросов в инструменте аналитики.

Вы сами определяете частоту передачи и решаете, как скоро данные должны быть доступны для запросов. Данные лучше передавать блоками большого объема, но не более 1 ГБ.

> [!NOTE]
> *Если у вас много источников данных для анализа*, [*попробуйте использовать* конвейер logstash *для отправки данных в Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Перед началом работы

Вам необходимы:

1. Ресурс Application Insights в Microsoft Azure.

 * Если вы хотите анализировать данные отдельно от других данных телеметрии, [создайте новый ресурс Application Insights](app-insights-create-new-resource.md).
 * Если вы объединяете или сравниваете данные с данными телеметрии из приложения, для которого уже настроена надстройка Application Insights, вы можете использовать ресурс этого приложения.
 * Права владельца или участника на доступ к этому ресурсу.
 
2. хранилище Azure. Вы передаете данные в службу хранилища Azure, а инструмент аналитики получает данные из него. 

 * Рекомендуем создать отдельную учетную запись хранения для больших двоичных объектов. Если большие двоичные объекты используются другими процессами, нашим процессам понадобится больше времени для чтения этих объектов.


## <a name="define-your-schema"></a>Определение схемы

Прежде чем импортировать данные, необходимо определить *источник данных*, в котором указана схема данных.
В отдельном ресурсе Application Insights может быть до 50 источников данных.

1. Запустите мастер источников данных. Воспользуйтесь кнопкой Add new data source (Добавить новый источник данных). Кроме того, нажмите кнопку "Параметры" в правом верхнем углу и в раскрывающемся меню выберите "Источники данных".

    ![Добавление источника данных](./media/app-insights-analytics-import/add-new-data-source.png)

    Введите имя для нового источника данных.

2. Определите формат файлов, которые требуется передать.

    Можно задать формат вручную или передать пример файла.

    Если используются данные в формате CSV, то первая строка примера может содержать заголовки столбцов. Вы сможете изменить имена полей на следующем шаге.

    Пример должен содержать по крайней мере 10 строк или записей данных.

    Имена столбцов или полей должны состоять из букв и цифр (без пробелов и знаков препинания).

    ![Передача примера файла](./media/app-insights-analytics-import/sample-data-file.png)


3. Проверьте схему, которую получил мастер. Если он вывел типы из примера, то, возможно, потребуется настроить выводимые типы столбцов.

    ![Просмотр выводимой схемы](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Необязательно.) Отправьте определение схемы. Формат приведен ниже.

 * Выберите метку времени. Все данные в инструменте аналитики должны включать поле метки времени. Это должно быть поле даты и времени (`datetime`), но его не обязательно называть timestamp. Если данные содержат столбец, содержащий дату и время в формате ISO, выберите его в качестве столбца метки времени. Или выберите "по мере поступления данных". В этом случае процесс импорта добавит полу метки времени.

5. Создайте источник данных.

### <a name="schema-definition-file-format"></a>Формат файла определения схемы

Вместо того, чтобы редактировать схему в пользовательском интерфейсе, загрузите определение схемы из файла. Определение схемы имеет следующий формат: 

Формат с разделителями 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

Формат JSON 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Каждый столбец идентифицируется по расположению, имени и типу. 

* Расположение: для формата с разделителями это расположение сопоставленного значения. Для формата JSON это путь jpath сопоставленного ключа.
* Имя: отображаемое имя столбца.
* Тип: тип данных столбца.
 
Если использовался пример данных и формат файла с разделителями, то определение схемы должно сопоставить все столбцы и добавить в конце новые столбцы. 

Формат JSON позволяет выполнить частичное сопоставление данных, поэтому определение схемы в формате JSON не должно сопоставлять каждый ключ, найденный в примере данных. Оно также может сопоставить столбцы, которые не являются частью примера данных. 

## <a name="import-data"></a>Импорт данных

Чтобы импортировать данные, передайте их в службу хранилища Azure, создайте ключ доступа и вызовите REST API.

![Добавление источника данных](./media/app-insights-analytics-import/analytics-upload-process.png)

Вы можете выполнить описанный ниже процесс вручную или настроить автоматизированную систему, которая будет делать это регулярно. Выполните следующие шаги для каждого блока данных, которые нужно импортировать.

1. Отправьте данные в [хранилище BLOB-объектов Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Размер больших двоичных объектов в несжатом виде не должен превышать 1 ГБ. С точки зрения производительности идеально использовать большие двоичные объекты размером несколько сотен мегабайт.
 * Чтобы сократить время передачи и минимизировать задержку доступности данных для запроса, сожмите данные с помощью Gzip. Используйте расширение файла `.gz`.
 * Для этой цели рекомендуется использовать отдельную учетную запись хранения, чтобы избежать вызовов от разных служб, снижающих производительность.
 * При отправке данных с высокой частотой (каждые несколько секунд) рекомендуется использовать более одной учетной записи хранения, чтобы повысить производительность.

 
2. [Создайте ключ подписанного URL-адреса для большого двоичного объекта](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Ключ должен иметь срок действия один день и предоставлять доступ для чтения.
3. Вызовите REST, чтобы уведомить Application Insights о данных, которые ожидают приема.

 * Конечная точка: `https://dc.services.visualstudio.com/v2/track`
 * Метод HTTP: POST
 * Полезные данные:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Заполнители:

* `Blob URI with Shared Access Key` — вы получаете этот заполнитель во время создания ключа. Он относится к большому двоичному объекту.
* `Schema ID` — идентификатор схемы, созданный для определенной схемы. Данные в большом двоичном объекте должны соответствовать схеме.
* `DateTime` — время отправки запроса в формате UTC. Мы принимаем следующие форматы: ISO8601 (например, "2016-01-01 13:45:01"); RFC822 ("Wed, 14 Dec 16 14:57:01 +0000"); RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC"); RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000").
* `Instrumentation key` для ресурса Application Insights.

Данные будут доступны в аналитике через несколько минут.

## <a name="error-responses"></a>Сообщения об ошибках

* **400. Недопустимый запрос** — означает, что запрос содержит недопустимые полезные данные. Убедитесь, что:
 * указан правильный ключ инструментирования;
 * указано допустимое значение времени (должно быть указано текущее время UTC);
 * Данные JSON события соответствуют схеме.
* **403. Запрещено** — отправленный большой двоичный объект недоступен. Убедитесь, что общий ключ доступа является допустимым и срок его действия не истек.
* **404. Не найдено**:
 * большой двоичный объект не существует;
 * Неправильный идентификатор sourceId.

Подробные сведения см. в сообщении об ошибке.


## <a name="sample-code"></a>Пример кода

Этот код использует пакет NuGet [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1).

### <a name="classes"></a>Классы

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Прием данных

Используйте этот код для каждого большого двоичного объекта. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Дополнительная информация

* [Знакомство с аналитикой в Application Insights](app-insights-analytics-tour.md)
* Если вы используете Logstash, используйте [подключаемый модуль Logstash для отправки данных в Application Insights](https://github.com/Microsoft/logstash-output-application-insights)

<properties 
	pageTitle="Как использовать веб-службу Машинного обучения, опубликованную из эксперимента Машинного обучения | Azure" 
	description="После публикации службы машинного обучения доступную веб-службу RESTFul можно использовать в качестве службы обработки запросов и ответов или службы пакетного выполнения." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# Как использовать опубликованную веб-службу Машинного обучения Azure

## Введение

При публикации в качестве веб-службы эксперименты Машинного обучения Azure предоставляют REST API, который может использоваться различными устройствами и платформами. Это происходит благодаря тому, что простой REST API принимает сообщения в формате JSON и отвечает на них в таком же формате. Портал машинного обучения Azure предоставляет код, который может использоваться для вызова веб-службы на языках R, C# и Python. Однако эти службы можно вызывать с помощью любого языка программирования и с любого устройства, которое удовлетворяет трем условиям:

* подключены к сети;
* поддерживают возможности SSL для выполнения HTTPS-запросов;
* поддерживают синтаксический анализ сообщений в формате JSON (вручную или с помощью библиотек поддержки).

Если все три условия удовлетворены, службы можно использовать из веб-приложений, мобильных приложений, пользовательских классических приложений и даже из Excel.  

Веб-службу Машинного обучения Azure можно использовать двумя различными способами: в качестве службы обработки запросов и ответов или в качестве службы пакетного выполнения. В каждом из этих сценариев функции предоставляются через веб-службу RESTFul, которая становится доступной для использования после публикации эксперимента. Во избежание преждевременных и текущих затрат на аппаратные ресурсы можно развернуть в Azure веб-службу машинного обучения с конечной точкой веб-службы Azure, чтобы служба масштабировалась автоматически в зависимости от использования.

<!-- При публикации этой статьи исправьте ссылку и раскомментируйте текст.
Дополнительную информацию о том, как управлять конечными точками веб-службы Машинного обучения Azure с помощью REST API, см. в статье **Конечные точки веб-службы Машинного обучения Azure**. 
-->

Информацию о том, как создать и опубликовать веб-службу Машинного обучения Azure, 
см. в разделе [Публикация веб-службы Машинного обучения Azure][publish]. Пошаговое руководство по созданию эксперимента машинного обучения и его публикации см. в разделе [Разработка прогнозирующего решения с помощью Машинного обучения Azure][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Служба запрос-ответ (RRS)

Служба обработки запросов и ответов (RRS) - высокомасштабируемая веб-служба с малым временем задержки, используемая в качестве интерфейса для моделей без изменения состояния, созданных и опубликованных из эксперимента Студии машинного обучения Azure.

RRS принимает одиночную строку входных параметров и формирует на выходе тоже одиночную строку. выходная строка может содержать несколько столбцов.

Пример RRS - проверка подлинности приложения. В этом случае можно ожидать, что приложение будет установлено от сотен до миллионов раз. При запуске приложение отправляет вызов в службу RRS с соответствующими входными данными. Затем оно получает ответ проверки от службы, которая разрешает выполнение приложения или блокирует его.


## Служба пакетного выполнения (BES)
 
Служба пакетного выполнения (BES) - это служба, выполняющая асинхронную оценку большого пакета записей данных. Входные данные для BES содержат пакет записей из разнообразных источников, таких как большие двоичные объекты, таблицы в Azure, SQL Azure, HDInsight (например, результаты запроса Hive) и источники HTTP. Выходные данные для BES содержат результаты оценки. Результаты выводятся в файл в хранилище больших двоичных объектов Azure, а в качестве ответа возвращается конечная точка хранилища.

BES используется в тех случаях, когда ответы не требуются немедленно, например при регулярной запланированной оценке отдельных пользователей или устройств из Интернета вещей (IOT).

## Примеры
Работу служб RRS и BES можно продемонстрировать на примере веб-службы Azure. Эта служба будет использоваться в сценарии Интернета вещей. Для простоты наше устройство отправляет только одно значение, `cog_speed`, и получает один ответ. 

Для вызова службы RRS или BES нужны четыре части информации. Эта информация становится доступна на [страницах службы Машинного обучения Azure](https://studio.azureml.net) после публикации эксперимента. Щелкните ссылку "ВЕБ-СЛУЖБЫ" в левой части экрана, и вы увидите опубликованные службы. Чтобы получить информацию о конкретной службе, перейдите по ссылкам страницы справки по API для служб RRS и BES.

1.	**Ключ API службы**, доступный на главной странице службы
2.	**Универсальный код ресурса службы**, доступный на странице справки по API для выбранной службы
3.	Ожидаемый **текст запроса API**, доступный на странице справки по API для выбранной службы
4.	Ожидаемый **текст запроса API**, доступный на странице справки по API для выбранной службы.

В двух приведенных ниже примерах для демонстрации необходимого кода используется язык C#. Целевая платформа - классическая версия Windows 8. 

### Пример RRS
На странице справки по API кроме универсального кода ресурса вы будете вводить и выводить определения и примеры кода. Для данной конкретной службы вызываются входные данные API, которые являются полезными данными вызова API. 

**Пример запроса**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


Аналогично для этой службы вызывается также ответ API.

**Пример ответа**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

В нижней части страницы можно найти примеры кода. Ниже приведен пример кода для реализации C#. 
                   
**Пример кода**
	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### Пример BES
На странице справки по API кроме универсального кода ресурса вы найдете информацию о нескольких доступных вызовах. В отличие от службы RRS служба BES является асинхронной. Это означает, что API BES просто ставит в очередь задание, которое необходимо выполнить. Однако на самом деле этот интерфейс не выполняет задание, пока не будет получен ответ API. Разработчик может использовать службу BES для трех задач, описанных ниже.

1. Отправка задания пакетного выполнения
1. Получение состояния или результата задания пакетного выполнения
1. Удаление задания пакетного выполнения  

**1. Отправка задания пакетного выполнения**

Чтобы отправить задание пакетного выполнения, нужно предоставить информацию о том, где хранятся данные пакета. В этом примере подразумевается, что записи, для которых требуется выполнить пакетную оценку, находятся в файле большого двоичного объекта в учетной записи хранения.

Ответом для задания пакетного выполнения будет снова идентификатор задания, так как задание было выполнено асинхронно. Мы используем идентификатор задания, чтобы получить состояние и результаты задания позже.

**Пример запроса**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Пример ответа**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Пример кода**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. Получение состояния или результата задания пакетного выполнения**

Чтобы получить результат задания, сначала необходимо получить идентификатор задания, содержащийся в ответе на отправку задания. На самом деле для этого вызова API данные не вводятся. В нем используется немного измененный универсальный код ресурса BES и метод запроса. Вместо запроса POST он использует запрос GET, который указан после универсального кода ресурса, определенного на странице справки API.
 
Ответ, однако, разделен на несколько уровней.

**Полезные данные ответа**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` может принимать значения 0, 1, 2, 3 или 4 со следующей семантикой:

* 0	 - не запущено;
* 1	 - выполняется;
* 2	 - ошибка;
* 3	 - отменено;
* 4	 - выполнено.

Если задание не выполнено, `Result` будет иметь значение **null**. Если задание выполнено, `Result` будет выглядеть так: 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

Значение параметра Details содержит данные об ошибке, если таковые имеются.

**Пример кода**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. Удаление задания пакетного выполнения**              
Задание можно удалить даже после запуска. Причины удаления могут быть разными. Одна из них - слишком длительное выполнение задания. Чтобы удалить задание, сначала необходимо получить идентификатор задания, содержащийся в ответе на отправку задания.

На самом деле для этого вызова API данные не вводятся. В нем используется немного измененный универсальный код ресурса BES и метод запроса. Вместо запроса POST он использует запрос DELETE, который указан после универсального кода ресурса, определенного на странице справки API. Пример кода для удаления очень прост.


<!--HONumber=49-->
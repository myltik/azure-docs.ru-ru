<properties
	pageTitle="Использование веб-службы машинного обучения | Microsoft Azure"
	description="После развертывания службы машинного обучения доступную веб-службу RESTFul можно использовать в качестве службы обработки запросов и ответов или службы пакетного выполнения."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="05/22/2016"
	ms.author="garye" />


# Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения

## Введение

При публикации в качестве веб-службы эксперименты машинного обучения Azure предоставляют REST API, который может использоваться самыми различными устройствами и платформами. Это происходит благодаря тому, что простой REST API принимает сообщения в формате JSON и отвечает на них в таком же формате. Портал машинного обучения Azure предоставляет код, который может использоваться для вызова веб-службы на языках R, C# и Python. Однако эти службы можно вызывать с помощью любого языка программирования и с любого устройства, которое удовлетворяет трем условиям:

* подключены к сети;
* поддерживают возможности SSL для выполнения HTTPS-запросов;
* поддерживают синтаксический анализ сообщений в формате JSON (вручную или с помощью библиотек поддержки).

Если все три условия удовлетворены, службы можно использовать из веб-приложений, мобильных приложений, пользовательских классических приложений и даже из Excel.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Веб-службу Машинного обучения Azure можно использовать двумя различными способами: в качестве службы обработки запросов и ответов или в качестве службы пакетного выполнения. В каждом из этих сценариев функции предоставляются через веб-службу RESTFul, которая становится доступной для использования после развертывания эксперимента. Во избежание преждевременных и текущих затрат на аппаратные ресурсы можно развернуть в Azure веб-службу машинного обучения с конечной точкой веб-службы Azure, чтобы служба масштабировалась автоматически в зависимости от использования.

> [AZURE.TIP] Простой способ создания веб-приложения для доступа к прогнозирующей веб-службе описан в статье [Использование веб-службы машинного обучения Azure с шаблоном веб-приложения](machine-learning-consume-web-service-with-web-app-template.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

Сведения о том, как создать и развернуть веб-службу машинного обучения Azure, см. в разделе [Развертывание веб-службы машинного обучения Azure][publish]. Пошаговое руководство по созданию эксперимента машинного обучения и его развертыванию см. в разделе [Разработка прогнозирующего решения с помощью машинного обучения Azure][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Служба запрос-ответ (RRS)

Служба обработки запросов и ответов (RRS) — высокомасштабируемая веб-служба с малым временем задержки, используемая в качестве интерфейса для моделей без изменения состояния, созданных и развернутых из эксперимента студии машинного обучения Azure. Она позволяет использовать сценарии, в которых потребляющее приложение ожидает ответа в режиме реального времени.

Служба запрос-ответ (RRS) принимает одну строку или несколько строк входных параметров и может формировать результат в одну или несколько строк. Выходные строки могут содержать несколько столбцов.

Пример RRS — проверка подлинности приложения. В этом случае можно ожидать, что приложение будет установлено от сотен до миллионов раз. При запуске приложение отправляет вызов в службу RRS с соответствующими входными данными. Затем оно получает ответ проверки от службы, которая разрешает выполнение приложения или блокирует его.


## Служба пакетного выполнения (BES)

Служба пакетного выполнения (BES) — это служба, выполняющая асинхронную оценку большого пакета записей данных. Входные данные для BES содержат пакет записей из разнообразных источников, таких как большие двоичные объекты, таблицы в Azure, SQL Azure, HDInsight (например, результаты запроса Hive) и источники HTTP. Выходные данные для BES содержат результаты оценки. Результаты выводятся в файл в хранилище больших двоичных объектов Azure, а в качестве ответа возвращается конечная точка хранилища.

BES используется в тех случаях, когда ответы не требуются немедленно, например при регулярной запланированной оценке отдельных пользователей или устройств из Интернета вещей (IOT).

## Примеры
Работу служб RRS и BES можно продемонстрировать на примере веб-службы Azure. Эта служба будет использоваться в сценарии Интернета вещей. Для простоты наше устройство отправляет только одно значение, `cog_speed`, и получает один ответ.

Для вызова службы RRS или BES нужны четыре части информации. Эта информация доступна на страницах службы в [Студии машинного обучения Azure](https://studio.azureml.net) после развертывания эксперимента. Щелкните вкладку ВЕБ-СЛУЖБЫ в левой части экрана для просмотра развернутых служб. Щелкните службу, чтобы найти следующие ссылки и сведения о службах RRS и BES.

1.	**Ключ API** службы, доступный на панели мониторинга службы.
2.	**URI запроса** службы, доступный на странице справки по API для выбранной службы.
3.	Ожидаемые **заголовки запроса** и **текст запроса** API, доступные на странице справки по API для выбранной службы.
4.	Ожидаемые **заголовки ответа** и **текст ответа** API, доступные на странице справки по API для выбранной службы.

В двух приведенных ниже примерах для демонстрации необходимого кода используется язык C#. Целевая платформа — классическая версия Windows 8.

### Пример RRS
Щелкните **ЗАПРОС/ОТВЕТ** под **страницей справки API** на панели мониторинга службы, чтобы просмотреть страницу справки API. На этой странице кроме универсального кода ресурса находятся определения входных и выходных данных и примеры кода. Для данной конкретной службы показаны входные данные API, которые являются полезными данными вызова API.

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


Ниже также показан ответ API для этой службы.

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

В нижней части страницы справки находятся примеры кода. Далее приведен пример кода для реализации C#.

**Пример кода на языке C#**

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

**Пример кода на языке Java**

В следующем примере кода показано, как создать запрос REST API на языке Java. Предполагается, что переменные (apikey и apiurl) содержат необходимые сведения об API, а переменная jsonBody — соответствующий объект JSON, который требуется REST API для составления успешного прогноза. Загрузить весь код полностью можно по адресу [https://github.com/nk773/AzureML\_RRSApp](https://github.com/nk773/AzureML_RRSApp). В этом примере кода Java требуется [клиентская библиотека HTTP Apache](https://hc.apache.org/downloads.cgi).

	/**
	 * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
 	 */
    	/**
     	  * Call REST API for retrieving prediction from Azure ML 
     	  * @return response from the REST API
     	  */	
    	public static String rrsHttpPost() {
        
        	HttpPost post;
        	HttpClient client;
        	StringEntity entity;
        
        	try {
            		// create HttpPost and HttpClient object
            		post = new HttpPost(apiurl);
            		client = HttpClientBuilder.create().build();
            
            		// setup output message by copying JSON body into 
            		// apache StringEntity object along with content type
            		entity = new StringEntity(jsonBody, HTTP.UTF_8);
            		entity.setContentEncoding(HTTP.UTF_8);
            		entity.setContentType("text/json");

            		// add HTTP headers
            		post.setHeader("Accept", "text/json");
            		post.setHeader("Accept-Charset", "UTF-8");
        
            		// set Authorization header based on the API key
            		post.setHeader("Authorization", ("Bearer "+apikey));
            		post.setEntity(entity);

            		// Call REST API and retrieve response content
            		HttpResponse authResponse = client.execute(post);
            
            		return EntityUtils.toString(authResponse.getEntity());
            
        	}
        	catch (Exception e) {
            
            		return e.toString();
        	}
    
    	}
    
    	
 

### Пример BES
В отличие от службы RRS служба BES является асинхронной. Это означает, что BES API просто ставит в очередь задание, а вызывающий объект запрашивает состояние задания, чтобы увидеть, когда оно будет завершено. Ниже перечислены операции, которые в настоящее время поддерживаются для пакетных заданий:

1. Создать (отправить) пакетное задание
1. Запустить это пакетное задание
1. Получить состояние или результат пакетного задания
1. Отменить выполнение пакетного задания

**1. Создать задание пакетного выполнения**

При создании пакетного задания для конечной точки службы машинного обучения Azure можно указать несколько параметров, определяющих выполнение этого пакета.

* **Входные данные**: представляет собой ссылку на BLOB-объект, где хранятся входные данные пакетного задания.
* **Глобальные параметры**: представляет собой набор глобальных параметров, которые можно задать для эксперимента. Эксперимент машинного обучения Azure может иметь как обязательные, так и необязательные параметры, определяющие выполнение службы. При этом вызывающий объект должен предоставлять все обязательные параметры (если применимо). Эти параметры задаются в виде коллекции пар «ключ-значение».
* **Выходные данные**: если служба определила один или несколько массивов выходных данных, вызывающий объект может перенаправлять любой из них по месту расположения BLOB-объекта Azure. Это позволит сохранить выходные данные службы в желаемом месте и с предсказуемым именем. В противном случае имя выходного BLOB-объекта выбирается случайным образом. 

    Обратите внимание, что служба ожидает выходное содержимое исходя из его типа с сохранением в поддерживаемых форматах:
  - выходные данные набора данных: можно сохранить как **.csv, .tsv, .arff**;
  - выходные данные обученной модели: можно сохранить как **.ilearner**.

  Переопределенные места расположения выходных данных указываются как коллекция пар *<output name  blob reference>*, где *имя выходных данных* определяется пользователем по конкретным узлам выходных данных (см. также страницу справки по API службы), а *ссылка на BLOB-объект* представляет собой ссылку на место расположения BLOB-объекта Azure, в которое будут перенаправляться выходные данные.

Все эти параметры создания задания могут быть необязательным в зависимости от службы. Например, службы, у которых входной узел не определен, не требуют передачи параметра *Входные данные*. Возможность переопределения расположения выходных данных является необязательной, поскольку выходные данные будут храниться в учетной записи хранения по умолчанию, которая была создана для рабочей области машинного обучения Azure. Ниже показан пример запроса полезных данных, перенесенных в REST API, для службы, в которую были перенесены только входные данные.

**Пример запроса**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

Ответ на API создания пакетного задания — это уникальный идентификатор задания, привязываемый к заданию. Данный идентификатор весьма важен, поскольку он обеспечивает исключительную возможность создания ссылки на это задание в системе для других операций.

**Пример ответа**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Запуск задачи пакетного выполнения**

Создаваемое пакетное задание лишь регистрируется в системе и переводится в состояние *Не запущено*. Чтобы фактически запланировать задание к выполнению, необходимо вызвать API **запуска**, как указано на странице справки по API конечной точки службы, и указать идентификатор задания, полученный при создании задания.

**3. Получение состояния задания пакетного выполнения**

В любой момент можно запросить состояние асинхронного пакетного задания, передав идентификатор задания в API получения состояния задания. В ответе API будет указано текущее состояние задания, а также фактические результаты выполнения пакетного задания при условии его успешного завершения. В случае ошибки сведения о фактических причинах сбоя приводятся в свойстве *Подробности*, как показано далее.

**Полезные данные ответа**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

Параметр *StatusCode* может иметь одно из следующих значений:

* Не запущено
* Выполнение
* Сбой
* Отменено
* Выполнено

Свойство *Результаты* заполняется только в том случае, если задание успешно завершено (в противном случае оно остается **пустым**). После завершения задания, если служба имеет хотя бы один определенный выходной узел, результаты выдаются в виде коллекции пар *[имя выходных данных, ссылка на BLOB-объект]*, где ссылка на BLOB-объект является ссылкой типа "SAS только для чтения" на BLOB-объект, содержащий фактический результат.

**Пример ответа**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4. Отмена задания пакетного выполнения**

Выполняемое пакетное задание в любой момент можно отменить, вызвав API отмены задания и передав идентификатор задания. Причины удаления могут быть разными. Одна из них — слишком длительное выполнение задания.



#### Использование пакета SDK BES

[Пакет BES SDK Nugget](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) предоставляет функции, упрощающие вызов BES для оценки в пакетном режиме. Чтобы установить пакет Nuget в Visual Studio, перейдите в меню **Инструменты**, выберите **Диспетчер пакетов Nuget** и щелкните **Консоль диспетчера пакетов**.

Эксперименты машинного обучения Azure, которые развертываются как веб-службы, могут включать в себя входные модули веб-служб. Это означает, что ожидается предоставление входных данных через вызов веб-службы в форме ссылки на расположение BLOB-объекта. Кроме того, имеется возможность не использовать входной модуль веб-службы, а воспользоваться модулем **чтения**. В этом случае модуль **чтения**, как правило, считывает данные из БД SQL посредством запроса в процессе выполнения для получения данных. Параметры веб-службы можно использовать для динамического указания других серверов или таблиц и т. д. SDK поддерживает оба этих варианта.

В следующем примере кода показан способ отправки и мониторинга пакетного задания по конечной точке службы машинного обучения Azure посредством пакета BES SDK. Обратите внимание на комментарии касательно подробностей о параметрах и вызовах.

#### **Пример кода**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning

	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;

	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;

	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }

	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);

	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results

	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.

	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };

	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };

	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();

	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

#### Пример кода на языке Java для BES
Служба выполнения пакетов REST API принимает JSON, содержащий ссылку на CSV-файлы с примерами входных и выходных данных представленного ниже вида, и создает в Azure ML задание по составлению пакетных прогнозов. Полный код см. в [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp). В этом примере кода Java требуется [клиентская библиотека HTTP Apache](https://hc.apache.org/downloads.cgi).


	{ "GlobalParameters": {}, 
    	"Inputs": { "input1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=Q8kkieg==", 
        	"RelativeLocation": "myContainer/sampleinput.csv" } }, 
    	"Outputs": { "output1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
        	"RelativeLocation": "myContainer/sampleoutput.csv" } } 
	} 


#####Создание задания BES	
	    
	    /**
	     * Call REST API to create a job to Azure ML 
	     * for batch predictions
	     * @return response from the REST API
	     */	
	    public static String besCreateJob() {
	        
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(apiurl);
	            client = HttpClientBuilder.create().build();
	            
	            // setup output message by copying JSON body into 
	            // apache StringEntity object along with content type
	            entity = new StringEntity(jsonBody, HTTP.UTF_8);
	            entity.setContentEncoding(HTTP.UTF_8);
	            entity.setContentType("text/json");
	
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
				// note a space after the word "Bearer " - don't miss that
	            post.setHeader("Authorization", ("Bearer "+apikey));
	            post.setEntity(entity);
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll(""", "");
	            
	            
	            return jobId;
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    
	    }
	    
#####Запуск созданного ранее задания BES	        
	    /**
	     * Call REST API for starting prediction job previously submitted 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besStartJob(String job){
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
#####Отмена созданного ранее задания BES
	    
	    /**
	     * Call REST API for canceling the batch job 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besCancelJob(String job) {
	        HttpDelete post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpDelete(startJobUrl+job);
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	         
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
	    
###Другие среды программирования
Чтобы составить подобный код на другом языке программирования, воспользуйтесь документом Swagger со страницы справки по API и инструкциями, приведенными на веб-сайте [swagger.io](http://swagger.io/). Откройте веб-сайт [swagger.io](http://swagger.io/swagger-codegen/) и загрузите код Swagger, Java и Apache MVN согласно приведенным на сайте инструкциям. Ниже даны краткие инструкции по настройке Swagger для других сред программирования.

* Убедитесь, что на компьютере установлена Java версии 7 или более высокой.
* Установите Apache MVN (в Ubuntu можно использовать *apt-get install mvn*).
* Откройте Swagger на Github и загрузите проект Swagger в формате ZIP-файла.
* Распакуйте Swagger.
* Соберите средства Swagger, запустив *пакет mvn* из исходного каталога Swagger.

Теперь средства Swagger готовы к использованию. Ниже даны инструкции по созданию клиентского кода Java.

* Перейдите на страницу справки по API машинного обучения Azure (см. пример [здесь](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs)).
* Найдите URL-адрес файла swagger.json для API REST машинного обучения Azure (второй снизу маркер в верхней части страницы справки по API).
* Щелкните ссылку на документ Swagger (см. пример [здесь](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)).
* Создайте клиентский код, выполнив команду, указанную в [файле Read Me для Swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md).

**Пример командной строки для создания клиентского кода**

	java -jar swagger-codegen-cli.jar generate\
	 -i https://ussouthcentral.services.azureml.net:443/workspaces/\
	fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
	 -l java -o /home/username/sample

* Используя значения из полей, basePath и /swagger.json в представленном ниже примере Swagger для [страницы справки по Api](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument), составьте URL-адрес Swagger и вставьте его в приведенную выше строку команды.

**Пример страницы справки по API**


	{
	  "swagger": "2.0",
	  "info": {
	    "version": "2.0",
	    "title": "Sample 5: Binary Classification with Web Service: Adult Dataset [Predictive Exp.]",
	    "description": "No description provided for this web service.",
	    "x-endpoint-name": "default"
	  },
	  "host": "ussouthcentral.services.azureml.net:443",
	  "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
	  "schemes": [
	    "https"
	  ],
	  "consumes": [
	    "application/json"
	  ],
	  "produces": [
	    "application/json"
	  ],
	  "paths": {
	    "/swagger.json": {
	      "get": {
	        "summary": "Get swagger API document for the web service",
	        "operationId": "getSwaggerDocument",
	        

<!---HONumber=AcomDC_0525_2016-->
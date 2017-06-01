---
title: "Использование веб-службы машинного обучения | Документация Майкрософт"
description: "Развернув службу машинного обучения, доступную веб-службу RESTFul можно использовать в качестве службы обработки запросов и ответов или службы пакетного выполнения."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/05/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 650ac9e8607c7c3b26cc2eebfe78169331800edd
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения
## <a name="introduction"></a>Введение
При публикации в качестве веб-службы эксперименты машинного обучения Azure предоставляют REST API и сообщения в формате JSON, которые могут использоваться самыми разными устройствами и платформами. Портал машинного обучения Azure предоставляет код, который может использоваться для вызова веб-службы на языках R, C# и Python. 

Службы можно вызывать с помощью любого языка программирования и с любого устройства, которое удовлетворяет трем условиям:

* подключены к сети;
* поддерживают возможности SSL для выполнения HTTPS-запросов;
* поддерживают анализ данных в формате JSON (напрямую или с помощью библиотек поддержки).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Веб-службу машинного обучения Azure можно использовать двумя способами: в качестве службы обработки запросов и ответов или в качестве службы выполнения пакетов. В каждом из этих сценариев функции предоставляются через веб-службу RESTFul, которая становится доступной для использования после развертывания эксперимента.

> [!TIP]
> Простой способ создания веб-приложения для доступа к прогнозной веб-службе описан в статье [Использование веб-службы машинного обучения Azure с шаблоном веб-приложения](machine-learning-consume-web-service-with-web-app-template.md).
> 
> 
> Сведения о создании и развертывании веб-службы машинного обучения Azure см. в статье [Развертывание веб-службы машинного обучения Azure][publish]. Пошаговое руководство по созданию эксперимента машинного обучения и его развертыванию см. в [этой статье][walkthrough].

## <a name="request-response-service-rrs"></a>Служба запрос-ответ (RRS)
Служба обработки запросов и ответов (RRS) — это высокомасштабируемая веб-служба с малым временем задержки, используемая в качестве интерфейса для моделей без отслеживания состояния, созданных и развернутых из эксперимента студии машинного обучения Azure. Она позволяет использовать сценарии, в которых выполняющееся приложение ожидает ответ в режиме реального времени.

Служба запрос-ответ (RRS) принимает одну строку или несколько строк входных параметров и может формировать результат в одну или несколько строк. Выходные строки могут содержать несколько столбцов.

Пример RRS — проверка подлинности приложения. В этом случае можно ожидать, что приложение будет установлено от сотен до миллионов раз. При запуске приложение отправляет вызов в службу RRS с соответствующими входными данными. Затем оно получает ответ проверки от службы, которая разрешает выполнение приложения или блокирует его.

## <a name="batch-execution-service-bes"></a>Служба пакетного выполнения (BES)
Служба пакетного выполнения (BES) — это служба, выполняющая асинхронную оценку большого пакета записей данных. Входные данные для BES содержат пакет записей из разнообразных источников, включая большие двоичные объекты, таблицы в Azure, SQL Azure, HDInsight (например, результаты запроса Hive) и источники HTTP. Выходные данные для BES содержат результаты оценки. Результаты выводятся в файл в хранилище больших двоичных объектов Azure, а в качестве ответа возвращается конечная точка хранилища.

BES используется в тех случаях, когда ответы не требуются немедленно, например при регулярной запланированной оценке отдельных пользователей или устройств из Интернета вещей (IOT).

## <a name="examples"></a>Примеры
Работу служб RRS и BES можно продемонстрировать на примере веб-службы Azure. Эта служба будет использоваться в сценарии Интернета вещей. Для простоты наше устройство отправляет только одно значение, `cog_speed`, и получает один ответ.

После развертывания эксперимента нам потребуются четыре типа данных для вызова службы RRS или BES:

* **ключ API** или **первичный ключ** службы;
* **URI запроса** на обслуживание;
* ожидаемые **заголовки** и **текст** запроса API;
* ожидаемые **заголовки** и **текст** запроса API.

Способ поиска этих сведений зависит от типа развернутой службы (новая веб-служба или классическая веб-служба).

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>Поиск информации на портале веб-служб машинного обучения Azure
Чтобы найти требуемые сведения, сделайте следующее:

1. Войдите на портал [веб-служб машинного обучения Azure][webservicesportal].
2. Щелкните **Веб-службы** или **Classic Web Services** (Классические веб-службы).
3. Выберите веб-службу, с которой вы работаете. 
4. Если вы работаете с классической веб-службой, щелкните используемую конечную точку.

Нужные сведения можно найти на таких страницах:

* **первичный ключ** — на странице **Использование**;
* **URI запроса** — на странице **Использование**; 
* ожидаемые **заголовки запросов** API, **заголовки ответов** API, а также **текст запросов и ответов** API — на странице **Swagger API**.

### <a name="information-locations-in-machine-learning-studio-classic-web-service-only"></a>Поиск информации в студии машинного обучения (только для классических веб-служб)
Необходимые сведения можно найти в двух источниках: в студии машинного обучения или на портале веб-служб машинного обучения Azure.

Чтобы найти требуемую информацию в студии машинного обучения, сделайте следующее:

1. Войдите в [Студию машинного обучения][mlstudio].
2. В левой части экрана щелкните **Веб-службы**.
3. Выберите веб-службу, с которой вы работаете. 

Нужные сведения можно найти на таких страницах:

* **ключ API** — на **панели мониторинга** службы; 
* **URI запроса** — на странице справки по API;
* ожидаемые **заголовки запросов** API, **заголовки ответов** API, а также **текст запросов и ответов** API — на странице справки по API.

Для доступа к странице справки по API щелкните ссылку **Запрос-ответ** или **Выполнение пакета** в зависимости от задачи.

Чтобы найти нужную информацию на портале веб-служб машинного обучения Azure, сделайте следующее:

1. Войдите на портал [веб-служб машинного обучения Azure][webservicesportal].
2. Щелкните **Classic Web Services** (Классические веб-службы).
3. Выберите веб-службу, с которой вы работаете. 
4. Щелкните используемую конечную точку.

Нужные сведения можно найти на таких страницах:

* **первичный ключ** — на странице **Использование**;
* **URI запроса** — на странице **Использование**; 
* ожидаемые **заголовки запросов** API, **заголовки ответов** API, а также **текст запросов и ответов** API — на странице **Swagger API**.

В двух приведенных ниже примерах для демонстрации необходимого кода используется язык C#.

### <a name="rrs-example"></a>Пример RRS
В следующем примере запроса показано, как API вводит полезные данные для вызова API из нашего примера службы. Если используется классическая веб-служба, примеры полезных данных можно найти на **странице справки по API** или на странице **Swagger API** портала веб-служб машинного обучения. Если используется новая веб-служба, примеры полезных данных можно найти на странице **Swagger API** портала веб-служб машинного обучения.

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


Аналогичным образом в следующем примере показан ответ API для службы.

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
   }

Далее приведен пример кода для реализации C#. Примеры кода для классической веб-службы можно найти в нижней части **страницы справки по API** или в нижней части страницы **Использование**. Примеры кода для новой веб-службы можно найти в нижней части страницы **Использование**.

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

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
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

В следующем примере кода показано, как создать запрос REST API на языке Java. Предполагается, что переменные (apikey и apiurl) содержат необходимые сведения об API, а переменная jsonBody — соответствующий объект JSON, который требуется REST API. Скачать весь код полностью можно по адресу [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp). В этом примере кода Java требуется [клиентская библиотека HTTP Apache](https://hc.apache.org/downloads.cgi).

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




### <a name="bes-example"></a>Пример BES
В отличие от службы RRS служба BES является асинхронной. Это означает, что BES API просто ставит в очередь задание, а вызывающий объект запрашивает состояние задания, чтобы увидеть, когда оно будет завершено. Ниже перечислены операции, которые в настоящее время поддерживаются для пакетных заданий:

1. Создать (отправить) пакетное задание
2. Запустить это пакетное задание
3. Получить состояние или результат пакетного задания
4. Отменить выполнение пакетного задания

**1. Создать задание пакетного выполнения**

При создании пакетного задания для службы машинного обучения Azure можно указать несколько параметров, определяющих выполнение этого пакета.

* **Input**: представляет собой ссылку на BLOB-объект, где хранятся входные данные пакетного задания.
* **GlobalParameters**: представляет собой набор глобальных параметров, которые можно задать для эксперимента. Эксперимент машинного обучения Azure может иметь как обязательные, так и необязательные параметры, определяющие выполнение службы. При этом вызывающий объект должен предоставлять все обязательные параметры (если применимо). Эти параметры задаются в виде коллекции пар «ключ-значение».
* **Outputs**: если служба определила один или несколько массивов выходных данных, вызывающий объект может перенаправлять любой из них по месту расположения BLOB-объекта Azure. Указав этот параметр, можно сохранить выходные данные службы в нужном расположении и с предсказуемым именем. В противном случае имя выходного BLOB-объекта выбирается случайным образом. 
  
    Служба ожидает выходное содержимое исходя из его типа с сохранением в поддерживаемых форматах:
  
  * выходные данные набора данных: можно сохранить как **.csv, .tsv, .arff**
  * выходные данные обученной модели: необходимо сохранить в формате **ILEARNER**.
    
    Переопределения расположения выходных данных указываются как коллекция пар, состоящих из имени выходных данных и ссылки на большой двоичный объект. *Имя выходных данных* — это определяемое пользователем имя для конкретного выходного узла, а *ссылка на большой двоичный объект* — это ссылка на расположение больших двоичных объектов Azure, в которое перенаправляются выходные данные. *Имя выходных данных* отображается на странице справки по API в службе.

Все параметры, используемые при создании задания, могут быть необязательными в зависимости от службы. Например, службы, у которых входной узел не определен, не требуют передачи параметра *Input* . Аналогичным образом возможность переопределить расположение выходных данных является необязательной, так как выходные данные будут храниться в учетной записи хранения по умолчанию, которая была создана для рабочей области машинного обучения Azure. Ниже показан пример запроса полезных данных для службы, в которую были перенесены только входные данные.

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

Ответ на API создания пакетного задания — это уникальный идентификатор задания, привязываемый к заданию. Этот идентификатор весьма важен, так как он обеспечивает исключительную возможность создания ссылки на это задание в системе для других операций.  

**Пример ответа**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Запуск задачи пакетного выполнения**

Создаваемое пакетное задание регистрируется в системе и переводится в состояние *Не запущено*. Чтобы фактически запланировать задание к выполнению, необходимо вызвать API **запуска**, как указано на странице справки по API конечной точки службы или на странице Swagger API веб-службы, и указать идентификатор задания, полученный при создании задания.

**3. Получение состояния задания пакетного выполнения**

В любой момент можно запросить состояние асинхронного пакетного задания, передав идентификатор задания в API *GetJobStatus*. В ответе API будет указано текущее состояние задания, а также фактические результаты выполнения пакетного задания, в которых указано, успешно ли оно завершено. В случае ошибки сведения о фактических причинах сбоя приводятся в свойстве *Details*, как показано ниже.

**Полезные данные ответа**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* может иметь одно из следующих значений:

* Не запущено
* Выполнение
* Сбой
* Canceled
* Выполнено

Свойство *Результаты* заполняется, только если задание успешно завершено (в противном случае оно остается **пустым**). Если для службы определен хотя бы один выходной узел, после завершения задания результаты возвращаются в виде коллекции пар *[имя выходных данных, ссылка на большой двоичный объект]*, где ссылка на большой двоичный объект — это предназначенная только для чтения ссылка SAS на большой двоичный объект, содержащий результат.

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

Выполняемое пакетное задание в любой момент можно отменить, вызвав API *CancelJob* и передав идентификатор задания. Причины отмены могут быть разными. Одна из них — слишком долгое выполнение задания.

#### <a name="using-the-bes-sdk"></a>Использование пакета SDK BES
[Пакет BES SDK Nuget](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) предоставляет функции, упрощающие вызов BES для оценки в пакетном режиме. Чтобы установить пакет Nuget, в Visual Studio щелкните меню **Средства**, выберите **Диспетчер пакетов Nuget** и щелкните **Консоль диспетчера пакетов**.

Эксперименты машинного обучения Azure, которые развертываются как веб-службы, могут включать входные модули веб-служб. Это означает, что входные данные предоставляются через вызов веб-службы в форме ссылки на расположение BLOB-объекта. Кроме того, можно не использовать входной модуль веб-службы, а воспользоваться модулем **Импорт данных**. В этом случае модуль **Импорт данных** считывает данные из источника, например базы данных SQL, при запросе во время выполнения. Параметры веб-службы можно использовать для динамического указания других серверов или таблиц и т. д. SDK поддерживает оба этих варианта.

В следующем примере кода показан способ отправки и мониторинга пакетного задания по службе машинного обучения Azure с использованием пакета BES SDK. Комментарии содержат подробные сведения о параметрах и вызовах.

#### <a name="sample-code"></a>**Пример кода**
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
                // First collect and fill in the URI and access key for your Web service endpoint.
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

#### <a name="sample-code-in-java-for-bes"></a>Пример кода на языке Java для BES
REST API службы выполнения пакетов принимает данные JSON, содержащие ссылку на CSV-файлы с примерами входных и выходных данных, как показано ниже, и создает в службе машинного обучения Azure задание по составлению пакетных прогнозов. Полный код см. на сайте [GitHub](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp). В этом примере кода Java требуется [клиентская библиотека HTTP Apache](https://hc.apache.org/downloads.cgi). 

    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>Создание задания BES
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

                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");


                return jobId;

            }
            catch (Exception e) {

                return e.toString();
            }

        }

##### <a name="start-a-previously-created-bes-job"></a>Запуск созданного ранее задания BES
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
##### <a name="cancel-a-previously-created-bes-job"></a>Отмена созданного ранее задания BES
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

### <a name="other-programming-environments"></a>Другие среды программирования
Чтобы составить подобный код на другом языке программирования, воспользуйтесь инструкциями, приведенными на веб-сайте [swagger.io](http://swagger.io/). Документ swagger для классической веб-службы можно получить:

* на странице справки по API; 
* с помощью вызова документа по API для конечной точки, который можно найти на странице Swagger на портале веб-служб машинного обучения. 

Откройте веб-сайт [swagger.io](http://swagger.io/swagger-codegen/) и скачайте код Swagger, Java и Apache MVN согласно приведенным на сайте инструкциям. 

Ниже приведены краткие инструкции по настройке Swagger для других сред программирования.

* Убедитесь, что на компьютере установлена Java версии 7 или более высокой.
* Установите Apache MVN (в Ubuntu можно использовать команду *apt-get install mvn*).
* Откройте Swagger на Github и загрузите проект Swagger в формате ZIP-файла.
* Распакуйте Swagger.
* Выполните сборку инструментов Swagger, выполнив команду *mvn package* из исходного каталога Swagger.

Теперь средства Swagger готовы к использованию. Ниже даны инструкции по созданию клиентского кода Java. 

* Перейдите на страницу справки по API машинного обучения Azure (см. пример [здесь](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs)).
* Найдите URL-адрес файла swagger.json для API REST машинного обучения Azure (второй снизу маркер в верхней части страницы справки по API).
* Щелкните ссылку на документ Swagger (см. пример [здесь](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument)).
* Создайте клиентский код, выполнив команду, указанную в [файле сведений Swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) .

**Пример командной строки для создания клиентского кода**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* Используя значения из полей host, basePath и /swagger.json в представленном ниже примере [страницы справки по API](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument) Swagger, составьте URL-адрес Swagger и вставьте его в приведенную выше командную строку.

**Пример страницы справки по API**

    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
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
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",

<!-- Relative Links -->

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net


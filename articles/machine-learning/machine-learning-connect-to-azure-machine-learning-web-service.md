<properties 
	pageTitle="Подключения к веб-службе Машинного обучения Azure | Azure" 
	description="При использовании C# или Python для подключения к веб-службе Машинного обучения Azure используется ключ авторизации." 
	services="machine-learning" 
	documentationCenter="" 
	authors="derrickv" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="derrickv" />


# Подключение к веб-службе Машинного обучения Azure 
Среда разработки Машинное обучение Azure - это API веб-службы, предназначенный для прогнозирования на основе входных данных в режиме реального времени или в пакетном режиме. Студия машинного обучения Azure используется для прогнозирования и публикации веб-службы Машинного обучения Azure. 

Информацию о том, как создать и опубликовать веб-службу Машинного обучения Azure с помощью Студии машинного обучения Azure, см. здесь:

- [Обзор процесса Машинного обучения Azure](../machine-learning-overview-of-azure-ml-process.md)
- [Начало работы со Студией машинного обучения](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Предварительный просмотр Машинного обучения Azure](https://studio.azureml.net/)
- [Центр документации машинного обучения](http://azure.microsoft.com/documentation/services/machine-learning/)

## Веб-служба Машинного обучения Azure ##

С помощью веб-службы Машинного обучения Azure (сокращенно - машинное обучение) внешнее приложение взаимодействует с рабочим процессом машинного обучения, оценивая модель в режиме реального времени. Вызов веб-службы машинного обучения возвращает результаты прогноза внешнему приложению. Чтобы вызвать веб-службу машинного обучения, следует передать ключ API, создаваемый при публикации прогноза. Веб-служба машинного обучения работает на основе архитектуры REST, используемой в основном в проектах с веб-программированием.

Машинное обучение Azure содержит два типа служб:

- Служба обработки запросов и ответов (RRS) - высокомасштабируемая служба с малым временем задержки, используемая в качестве интерфейса для моделей без изменения состояния, которые создаются и публикуются в Студии машинного обучения.
- Служба пакетного выполнения (BES) - асинхронная служба, оценивающая пакет записей данных.

Дополнительную информацию о веб-службах Машинного обучения Azure см. в статье [Обзор процесса Машинного обучения Azure](../machine-learning-overview-of-azure-ml-process.md).

## Получение ключа авторизации Машинного обучения Azure ##
Ключ API веб-службы можно получить в веб-службе машинного обучения. Это можно сделать из Студии машинного обучения Microsoft Azure или на портале управления Azure.
### Студия машинного обучения Microsoft Azure ###
1. В Студии машинного обучения Microsoft Azure щелкните **ВЕБ-СЛУЖБЫ** слева.
2. Щелкните веб-службу. "Ключ API" находится на вкладке **ПАНЕЛЬ МОНИТОРИНГА**.

### Портал управления Azure ###

1. Щелкните **МАШИННОЕ ОБУЧЕНИЕ** слева.
2. Щелкните рабочую область.
3. Щелкните **ВЕБ-СЛУЖБЫ**.
4. Щелкните веб-службу.
5. Щелкните конечную точку. Элемент "КЛЮЧ API" находится в правом нижнем углу.

## <a id="connect"></a>Подключение к веб-службе Машинного обучения Azure

К веб-службе Машинного обучения Azure можно подключиться, используя любой язык программирования, поддерживающий HTTP-запрос и HTTP-ответ. Примеры на языках C#, Python и R можно просмотреть на странице справки веб-службы Машинного обучения Azure.

### Просмотр страницы справки API веб-службы Машинного обучения Azure ###
Страница справки API Машинного обучения Azure создается при публикации веб-службы. См. раздел [Публикация веб-службы](machine-learning-walkthrough-5-publish-web-service.md) в пошаговом руководстве по Машинному обучению Azure.


**Чтобы просмотреть страницу справки API Машинного обучения Azure**
в Студии машинного обучения Microsoft Azure:

1. Выберите **ВЕБ-СЛУЖБЫ**.
2. Выберите веб-службу.
3. Выберите **Страница справки API** - **ЗАПРОС-ОТВЕТ** или **ПАКЕТНОЕ ВЫПОЛНЕНИЕ**.


**Страница справки API Машинного обучения Azure**.
На странице справки API Машинного обучения Azure содержится подробная информация о веб-службе прогнозирования, в том числе следующие.


<table>
	<tr>
		<td>&nbsp;</td>
		<td>Пример </td>
	</tr>
	<tr>
		<td>Универсальный код ресурса запроса POST </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>Пример запроса </td>
		<td>{ <br/> 
			&nbsp;&nbsp; "Id": "score00001",   <br/>
			&nbsp;&nbsp; "Instance": <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; {  <br/>  
 			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; "FeatureVector": { <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col1": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col2": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col3": "0", <br/>  
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  ...     },   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp;   "GlobalParameters": {}   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Текст ответа </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Имя</B></td>
				<td><B>Тип данных</B></td>
			</tr>
	
			<tr>
				<td>Feature</td>
				<td>Строковый</td>
			</tr>
			<tr>
				<td>Count</td>
				<td>Числовой</td>
			</tr>
			<tr>
				<td>Unique Value Count </td>
				<td>Числовой </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Пример ответа </td>
		<td>[&quot;Столбец1&quot;,&quot;1&quot;,&quot;1&quot;,...] </td>
	</tr>
	<tr>
		<td>Пример кода </td>
		<td>(Пример кода на C#, Python и R) </td>
	</tr>
</table>

**ПРИМЕЧАНИЕ.** Примеры взяты из части "Пример 1. Скачивание набора данных из UCI: набор данных Adult с 2 классами" в коллекции примеров Машинного обучения Azure.

### Пример на языке C# ###

Чтобы подключиться к веб-службе Машинного обучения Azure, используйте параметр **HttpClient**, передающий ScoreData. ScoreData содержит FeatureVector - n-мерный  вектор числовых признаков, представляющий ScoreData. Службу Машинного обучения Azure можно аутентифицировать с помощью ключа API.

Чтобы подключиться к веб-службе Машинного обучения, нужно установить пакет Nuget **Microsoft.AspNet.WebApi.Client**.

**Установка пакета Nuget Microsoft.AspNet.WebApi.Client в Visual Studio**

1. Опубликуйте веб-службу "Скачивание набора данных из UCI: набор данных Adult с 2 классами".
2. Щелкните **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
2. Выберите **Install-Package Microsoft.AspNet.WebApi.Client**.

**Выполнение примера кода**

1. Опубликуйте эксперимент "Пример 1. Скачивание набора данных из UCI: набор данных Adult с 2 классами", которых входит в коллекцию примеров Машинного обучения Azure.
2. Назначьте apiKey ключ из веб-службы. См. раздел "Как получить ключ авторизации Машинного обучения Azure".
3. Назначьте serviceUri универсальный код ресурса запроса. См. раздел "Как получить универсальный код ресурса запроса".

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Пример на Python ###

Чтобы подключиться к веб-службе Машинного обучения Azure, используйте библиотеку **urllib2**, передающую ScoreData. ScoreData содержит FeatureVector - n-мерный  вектор числовых признаков, представляющий ScoreData. Службу Машинного обучения Azure можно аутентифицировать с помощью ключа API.


**Выполнение примера кода**

1. Опубликуйте эксперимент "Пример 1. Скачивание набора данных из UCI: набор данных Adult с 2 классами", которых входит в коллекцию примеров Машинного обучения Azure.
2. Назначьте apiKey ключ из веб-службы. См. раздел "Как получить ключ авторизации Машинного обучения Azure".
3. Назначьте serviceUri универсальный код ресурса запроса. См. раздел "Как получить универсальный код ресурса запроса".

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 

<!--HONumber=49--> 
<properties 
	pageTitle="Подключитесь к веб-службе Машинного обучения | Microsoft Azure" 
	description="При использовании C# или Python для подключения к веб-службе Машинного обучения Azure используется ключ авторизации." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="derrickv" />


# Подключение к веб-службе Машинного обучения Azure 
Среда разработки Машинное обучение Azure — это API веб-службы, предназначенный для прогнозирования на основе входных данных в режиме реального времени или в пакетном режиме. Студия Машинного обучения Azure используется для создания прогнозов и их публикации в веб-службе Машинного обучения Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Чтобы узнать, как выполнять создание и публикацию в веб-службе Машинного обучения Azure с помощью Студии Машинного обучения Azure, обратитесь к следующим разделам:

- [Публикация в веб-службе Машинного обучения](machine-learning-publish-a-machine-learning-web-service.md)
- [Приступая к работе со Студией Машинного обучения](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Обзор Машинного обучения Azure](https://studio.azureml.net/)
- [Центр документации Машинного обучения Azure](http://azure.microsoft.com/documentation/services/machine-learning/)

## Веб-служба Машинного обучения Azure ##

С помощью веб-службы Машинного обучения Azure (сокращенно — машинное обучение) внешнее приложение взаимодействует с рабочим процессом машинного обучения, оценивая модель в режиме реального времени. Вызов веб-службы машинного обучения возвращает результаты прогноза внешнему приложению. Чтобы вызвать веб-службу машинного обучения, следует передать ключ API, создаваемый при публикации прогноза. Веб-служба машинного обучения работает на основе архитектуры REST, используемой в основном в проектах с веб-программированием.

Машинное обучение Azure содержит два типа служб:

- Служба обработки запросов и ответов (RRS) — высокомасштабируемая служба с малым временем задержки, используемая в качестве интерфейса для моделей без изменения состояния, которые создаются и публикуются в Студии машинного обучения.
- Служба пакетного выполнения (BES) — асинхронная служба, оценивающая пакет записей данных.

Дополнительные сведения о веб-службах Машинного обучения Azure приведены в разделе [Публикация в веб-службе Машинного обучения](machine-learning-publish-a-machine-learning-web-service.md).

## Получение ключа авторизации Машинного обучения Azure ##
Ключ API веб-службы можно получить в веб-службе машинного обучения. Это можно сделать из Студии машинного обучения Microsoft Azure или на портале управления Azure.
### Студия машинного обучения Microsoft Azure ###
1. В Студии Машинного обучения Microsoft Azure выберите **ВЕБ-СЛУЖБЫ** слева.
2. Щелкните веб-службу. Ключ API указан на вкладке **ПАНЕЛЬ УПРАВЛЕНИЯ**.

### Портал управления Azure ###

1. Щелкните **МАШИННОЕ ОБУЧЕНИЕ** слева.
2. Щелкните рабочую область.
3. Щелкните **ВЕБ-СЛУЖБЫ**.
4. Щелкните веб-службу.
5. Щелкните конечную точку. Элемент «КЛЮЧ API» находится в правом нижнем углу.

## <a id="connect"></a>Подключитесь к веб-службе Машинного обучения Azure

К веб-службе Машинного обучения Azure можно подключиться, используя любой язык программирования, поддерживающий HTTP-запрос и HTTP-ответ. Примеры на языках C#, Python и R можно просмотреть на странице справки веб-службы Машинного обучения Azure.

### Просмотр страницы справки API веб-службы Машинного обучения Azure ###
Страница справки API Машинного обучения Azure создается при публикации веб-службы. См. [Пошаговое руководство по Машинному обучению Azure - Публикация в веб-службе](machine-learning-walkthrough-5-publish-web-service.md).


**Для просмотра справочной страницы по API Машинного обучения Azure** выполните следующие действия в Студии машинного обучения Microsoft Azure:

1. Выберите **ВЕБ-СЛУЖБЫ**.
2. Выберите веб-службу.
3. Выберите **справочную страницу API** - **ЗАПРОС-ОТВЕТ** или **ВЫПОЛНЕНИЕ ПАКЕТА**.


**Справочная страница по API Машинного обучения Azure** Справочная страница по API Машинного обучения Azure содержит информацию о веб-службе прогнозирования, включая


<table>
	<tr>
		<td>&#160;</td>
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
			&#160;&#160; "Id": "score00001",   <br/>
			&#160;&#160; "Instance": <br/>
			&#160;&#160;&#160;&#160; {  <br/>  
 			&#160;&#160;&#160;&#160; &#160;&#160; "FeatureVector": { <br/>
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col1": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col2": "0", <br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col3": "0", <br/>  
			&#160;&#160;&#160;&#160; &#160;&#160;  ... },   <br/>
			&#160;&#160;&#160;&#160;   "GlobalParameters": {}   <br/>
			&#160;&#160;&#160;&#160; } <br/>
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
				<td>Функция</td>
				<td>Строка</td>
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
		<td>["Col1","1","1",…] </td>
	</tr>
	<tr>
		<td>Пример кода </td>
		<td>(Пример кода на C#, Python и R) </td>
	</tr>
</table>

**ПРИМЕЧАНИЕ** Данные примеры взяты из Примера 1: Загрузка набора данных из набора примеров UCI: Adult 2.

### Пример на языке C# ###

Для подключения к веб-службе Машинного обучения Azure воспользуйтесь **клиентом HTTP** для передачи Данных набора. Данные набора содержат Вектор свойств, n-мерный вектор числовых параметров, представляющий Данные набора. Службу Машинного обучения Azure можно аутентифицировать с помощью ключа API.

Для подключения к веб-службе Машинного обучения Azure должен быть установлен пакет Nuget **Microsoft.AspNet.WebApi.Client**.

**Установите Microsoft.AspNet.WebApi.Client Nuget в Visual Studio**

1. Опубликуйте набор данных Загрузки из набора UCI: Adult 2 веб-службы.
2. Выберите **Инструменты** > **Диспетчер пакетов Nuget** > **Консоль диспетчера пакетов**.
2. Выберите **Установить пакет Microsoft.AspNet.WebApi.Client**.

**Для запуска примера выполните следующие действия:**

1. Опубликуйте "Пример 1: Загрузка набора данных из UCI: Adult 2", входящий в набор примеров Машинного обучения Azure.
2. Назначьте apiKey ключ из веб-службы. См. раздел «Как получить ключ авторизации Машинного обучения Azure».
3. Назначьте serviceUri универсальный код ресурса запроса. См. раздел «Как получить универсальный код ресурса запроса».

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

Для подключения к веб-службе Машинного обучения воспользуйтесь библиотекой **urllib2** для передачи данных набора. Данные набора содержат Вектор свойств, n-мерный вектор числовых параметров, представляющий Данные набора. Службу Машинного обучения Azure можно аутентифицировать с помощью ключа API.


**Для запуска примера выполните следующие действия:**

1. Опубликуйте "Пример 1: Загрузка набора данных из UCI: Adult 2", входящий в набор примеров Машинного обучения Azure.
2. Назначьте apiKey ключ из веб-службы. См. раздел «Как получить ключ авторизации Машинного обучения Azure».
3. Назначьте serviceUri универсальный код ресурса запроса. См. раздел «Как получить универсальный код ресурса запроса».

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
 

<!---HONumber=July15_HO2-->
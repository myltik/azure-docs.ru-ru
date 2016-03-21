<properties
	pageTitle="API-интерфейсы машинного обучения — текстовая аналитика | Microsoft Azure"
	description="API-интерфейсы текстовой аналитики машинного обучения Microsoft можно использовать для анализа неструктурированного текста в таких задачах, как анализ мнений, извлечение ключевой фразы, определение языка и определение темы."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="onewth"/>


# API-интерфейсы машинного обучения: текстовая аналитика для мнений, извлечения ключевой фразы, определения языка и определения темы

## Обзор

API анализа текста — это набор [веб-служб](https://datamarket.azure.com/dataset/amla/text-analytics) для анализа текста, созданный с помощью машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ мнений, извлечение ключевой фразы, определение языка и определение темы. Для работы с этим API данные для обучения не нужны, поэтому можно просто использовать свои текстовые данные. Для обеспечения лучшего в своем классе прогнозирования этот API применяет собственные расширенные методы обработки естественного языка.

Анализ текста в действии можно увидеть на нашем [демонстрационном сайте](https://text-analytics-demo.azurewebsites.net/), где также находятся [примеры](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) реализации анализа текста в C# и Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## анализ мнений

API возвращает численную оценку между 0 и 1. Если оценка приближается к 1, то у текста позитивная тональность, а если к 0, то негативная. Оценка тональности создается с помощью методов классификации. Входные признаки, поступающие в классификатор, включают n-граммы, признаки, созданные на основе частеречной разметки, и векторное представление слов. В настоящее время поддерживается только английский язык.
 
## Извлечение ключевой фразы

API возвращает список строк с обозначением ключевых тем разговора во входном тексте. Мы используем методы из высокотехнологичного набора средств для обработки естественных языков Microsoft Office. В настоящее время поддерживается только английский язык.

## Определение языка

API возвращает определенный язык и численную оценку между 0 и 1. Если оценка приближается к 1, существует стопроцентная уверенность, что язык определен правильно. Поддерживается всего 120 языков.

## Определение темы

Это только что выпущенный API-интерфейс, который определяет основные темы для списка отправленных текстовых записей. Раздел определяется ключевой фразой, которая может содержать одно или несколько связанных слов. Этот API-интерфейс позволяет определять темы для количества текстовых записей от нескольких сотен до тысяч. При этом минимальное количество текстовых записей равно 100. Обратите внимание, что для этого API-интерфейса тарифицируется каждая транзакция для текстовой записи. Этот API-интерфейс хорошо работает с короткими, написанными человеком текстами, такими как обзоры и отзывы пользователей.

---

## Определение интерфейса API

### Заголовки

Убедитесь, что в запрос, который должен иметь следующий вид, включены правильные заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Ключ учетной записи можно найти в вашей учетной записи в [Azure Data Market](https://datamarket.azure.com/account/keys). Обратите внимание, что в настоящее время в качества формата входных и выходных данных принимается только JSON. XML не поддерживается.

---

## API-интерфейсы отдельного ответа

### GetSentiment

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Пример запроса**

В приведенном ниже вызове запрашивается тональность фразы "Hello World".

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Возвращается следующий ответ:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Пример запроса**

В приведенном ниже вызове запрашиваются ключевые фразы в тексте "Замечательный отель для проживания, с уникальным декором и доброжелательным персоналом.".

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Возвращается следующий ответ:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Пример запроса**

В приведенном ниже вызове GET запрашивается тональность ключевых фраз в тексте *Hello World*.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Возвращается следующий ответ:

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Необязательные параметры**

`NumberOfLanguagesToDetect` является необязательным параметром. Значение по умолчанию — 1.

---

## API-интерфейсы пакетной службы

Служба текстовой аналитики позволяет выполнять поиск на основе тональности и ключевых фраз в пакетном режиме. Обратите внимание, что каждая из записей считается как одна транзакция. Таким образом, при получении тональности для 1000 записей в одном запросе будет вычтено 1000 транзакций.

Обратите внимание, что введенные в систему идентификаторы — это идентификаторы, возвращенные системой. Веб-служба не проверяет уникальность идентификаторов. Ответственность за проверку уникальности несет вызывающий объект.


### GetSentimentBatch

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Пример запроса**

В приведенном ниже вызове POST запрашивается тональность следующих ключевых фраз в тексте запроса: "Hello World", "Hello Foo World" и "Hello My World".

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Тело запроса:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
	    {"Id":"2","Text":"hello foo world"},
	    {"Id":"3","Text":"hello my world"},
	]}

В приведенном ниже ответе отображается список показателей, связанных с идентификаторами текста:

	{
	  "odata.metadata":"<url>", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Пример запроса**

В приведенном ниже вызове запрашивается список тональностей ключевых фраз в следующих текстах:

* "It was a wonderful hotel to stay at, with unique decor and friendly staff";
* "It was an amazing build conference, with very interesting talks";
* "The traffic was terrible, I spent three hours going to the airport".

Этот запрос выполняется как вызов метода POST в конечную точку:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Тело запроса:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

В приведенном ниже ответе можно получить список ключевых фраз, связанных с идентификаторами текста:

	{ "odata.metadata":"<url>",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

В приведенном ниже вызове POST запрашивается определение языка для двух входных значений текста:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Тело запроса:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Возвращается следующий ответ, где в первом наборе входных данных определен английский язык, а в втором — французский.

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## API-интерфейсы определения темы

Это только что выпущенный API-интерфейс, который определяет основные темы для списка отправленных текстовых записей. Раздел определяется ключевой фразой, которая может содержать одно или несколько связанных слов. Обратите внимание, что для этого API-интерфейса тарифицируется каждая транзакция для текстовой записи.

Этот API-интерфейс позволяет определять темы для количества текстовых записей от нескольких сотен до тысяч. При этом минимальное количество текстовых записей равно 100.


### Темы — отправить задание

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Пример запроса**


В приведенном ниже вызове POST запрашиваются темы для набора 100 статей, отображаются первая и последняя статьи и включены две стоповых фразы.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Тело запроса:

	{"Inputs":[
		{"Id":"1","Text":"I loved the food at this restaurant"},
		...,
		{"Id":"100","Text":"I hated the decor"}
	],
	"StopPhrases":[
		"restaurant", “visitor"
	]}

В приведенном ниже ответе вы получаете JobId для отправленного задания:

	{
		"odata.metadata":"<url>",
		"JobId":"<JobId>"
	}

Список из одного слова или фраз, состоящих из нескольких слов, которые не нужно возвращать в качестве темы. Можно использовать для исключения слишком общих тем. Например, в наборе данных об обзорах отелей разумно использовать стоповые фразы "отель" и "хостел".

### Темы — опрос результатов задания

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Пример запроса**

Передайте JobId, возвращенный на шаге "Отправить задание", для получения результатов. Мы рекомендуем вызывать эту конечную точку каждую минуту, пока состояние в ответе не изменится на "Завершено". Для завершения задания потребуется около 10 минут, для заданий с несколькими тысячами записей потребуется больше времени.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Во время его обработки ответ будет иметь следующий вид:

	{
		"odata.metadata":"<url>",
		"Status":"Running",
 		"TopicInfo":[],
		"TopicAssignment":[],
		"Errors":[]
	}


API возвращает выходные данные в JSON в следующем формате:

	{
		"odata.metadata":"<url>",
		"Status":"Finished",
		"TopicInfo":[
		{
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Score":8.0,
			"KeyPhrase":"food"
		},
		...
		{
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Score":6.0,
			"KeyPhrase":"decor"
    		}
  		],
		"TopicAssignment":[
		{
			"Id":"1",
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Distance":0.7809
		},
		...
		{
			"Id":"100",
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Distance":0.8034
		}
		],
		"Errors":[]


Ниже приведены свойства для каждой части ответа:

**Свойства TopicInfo**

| Ключ | Описание |
|:-----|:----|
| TopicId | Уникальный идентификатор каждой темы. |
| Оценка | Количество записей, назначенных теме. |
| KeyPhrase | Обобщающее слово или фраза для темы. Может состоять из одного или нескольких слов. |

**Свойства TopicAssignment**

| Ключ | Описание |
|:-----|:----|
| Идентификатор | Идентификатор записи. Соответствует идентификатору, включенному во входные данные. |
| TopicId | Идентификатор раздела, которому была назначена запись. |
| Distance | Уверенность в том, что запись принадлежит к разделу. Более близкое к нулю значение означает более высокую степень уверенности. |

<!---HONumber=AcomDC_0309_2016-->
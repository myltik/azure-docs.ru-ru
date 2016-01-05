<properties
	pageTitle="API-интерфейсы машинного обучения — текстовая аналитика | Microsoft Azure"
	description="API-интерфейсы текстовой аналитики, предоставляемые машинным обучением Azure. API можно применять для анализа неструктурированного текста в таких задачах, как анализ тональности, извлечение ключевой фразы и определение языка."
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
	ms.date="11/17/2015"
	ms.author="onewth"/>


# API-интерфейсы машинного обучения: текстовая аналитика для тональности, извлечения ключевой фразы и определения языка

## Обзор

API анализа текста — это набор [веб-служб](https://datamarket.azure.com/dataset/amla/text-analytics) для анализа текста, созданный с помощью машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ тональности, извлечение ключевой фразы и определение языка. Для работы с этим API данные для обучения не нужны, поэтому можно просто использовать свои текстовые данные. Для обеспечения лучшего в своем классе прогнозирования этот API применяет собственные расширенные методы обработки естественного языка.

Анализ текста в действии можно увидеть на нашем [демонстрационном сайте](https://text-analytics-demo.azurewebsites.net/), где также находятся [примеры](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) реализации анализа текста в C# и Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## анализ мнений

API возвращает численную оценку между 0 и 1. Если оценка приближается к 1, то у текста позитивная тональность, а если к 0, то негативная. Оценка тональности создается с помощью методов классификации. Входные признаки, поступающие в классификатор, включают n-граммы, признаки, созданные на основе частеречной разметки, и векторное представление слов. В настоящее время поддерживается только английский язык.
 
## Извлечение ключевой фразы

API возвращает список строк с обозначением ключевых тем разговора во входном тексте. Мы используем методы из высокотехнологичного набора средств для обработки естественных языков Microsoft Office. В настоящее время поддерживается только английский язык.

## Определение языка

API возвращает определенный язык и численную оценку между 0 и 1. Если оценка приближается к 1, существует стопроцентная уверенность, что язык определен правильно. Поддерживается всего 120 языков.

---

## Определение интерфейса API

### Заголовки

Убедитесь, что в запрос, который должен иметь следующий вид, включены правильные заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Ключ учетной записи можно найти в вашей учетной записи в [Azure Data Market](https://datamarket.azure.com/account/keys).

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

`NumberOfLanguagesToDetect` является необязательным параметром. Значение по умолчанию — 1.

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
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
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

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
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

<!---HONumber=AcomDC_1125_2015-->
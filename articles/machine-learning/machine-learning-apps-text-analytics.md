<properties
	pageTitle="Приложение машинного обучения: анализ тональности | Microsoft Azure"
	description="API анализа текста — это набор средств анализа текста, созданный с помощью Машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ тональности и извлечение ключевой фразы."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Приложение машинного обучения: служба анализа текста для анализа тональности#
##Обзор
API анализа текста — это набор [веб-служб](https://datamarket.azure.com/dataset/amla/text-analytics) для анализа текста, созданный с помощью Машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ тональности и извлечение ключевой фразы. Для использования этого API данные для обучения не нужны, поэтому можно просто использовать свои текстовые данные. Сейчас поддерживается только английский язык. Этот API использует собственные расширенные методы обработки естественного языка.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## анализ мнений##
API возвращает численную оценку между 0 и 1. Если оценка приближается к 1, то у текста позитивная тональность, а если к 0, то негативная. Оценка тональности создается с помощью методов классификации. Входные признаки, поступающие в классификатор, включают n-граммы, признаки, созданные на основе частеречной разметки, и векторное представление слов.
 
## Извлечение ключевой фразы##
API возвращает список строк с обозначением ключевых тем разговора во входном тексте. Мы используем методы из высокотехнологичного набора средств для обработки естественных языков Microsoft Office.

## Определение интерфейса API##

###GetSentiment###

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Пример запроса**

В приведенном ниже вызове GET запрашивается тональность фразы *Hello World*.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Получить ключ учетной записи можно [здесь](https://datamarket.azure.com/account/keys).

**Пример ответа**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Пример запроса**

В приведенном ниже вызове GET запрашивается тональность ключевых фраз в тексте *It was a wonderful hotel to stay at, with unique decor and friendly staff*.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Получить ключ учетной записи можно [здесь](https://datamarket.azure.com/account/keys).


**Пример ответа**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Пример запроса**

В приведенном ниже вызове POST запрашивается тональность следующих ключевых фраз в тексте запроса: Hello World, Hello Foo World и Hello My World.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Текст:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


Заголовки:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


Получить ключ учетной записи можно [здесь](https://datamarket.azure.com/account/keys).

**Пример ответа**

В приведенном ниже ответе отображается список показателей, связанных с идентификаторами текста:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL-адрес**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Пример запроса**

В приведенном ниже вызове POST запрашивается список тональностей ключевых фраз в следующем тексте:

*It was a wonderful hotel to stay at, with unique decor and friendly staff*;
 
*It was an amazing build conference, with very interesting talks*;

*The traffic was terrible, I spent three hours going to the airport*.



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Текст:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Получить ключ учетной записи можно [здесь](https://datamarket.azure.com/account/keys).


**Пример ответа**

В приведенном ниже ответе можно получить список ключевых фраз, связанных с идентификаторами текста:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**Примечания, связанные с пакетной обработкой**

Введенные в систему идентификаторы — это идентификаторы, возвращенные системой. Веб-служба не проверяет уникальность идентификаторов. Ответственность за проверку уникальности несет вызывающий объект.
 

<!---HONumber=July15_HO1-->
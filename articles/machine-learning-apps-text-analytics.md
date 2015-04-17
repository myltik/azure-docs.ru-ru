<properties
	pageTitle="Приложение машинного обучения: Служба анализа текста для анализа тональности | Azure "
	description="API анализа текста - это набор средств анализа текста, созданный с помощью Машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ тональности и извлечение ключевой фразы."
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Служба анализа текста для машинного обучения#
##Обзор
API анализа текста - это набор [веб-служб]( https://datamarket.azure.com/dataset/amla/text-analytics) для анализа текста, созданный с помощью Машинного обучения Azure. API можно использовать для анализа неструктурированного текста в таких задачах, как анализ тональности и извлечение ключевой фразы. Для использования этого API данные для обучения не нужны, поэтому можно просто использовать свои текстовые данные. Сейчас поддерживается только английский язык. Этот API использует собственные расширенные методы обработки естественного языка.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
## анализ мнений##
API возвращает численную оценку между 0 и 1. Если оценка приближается к 1, то у текста позитивная тональность, а если к 0, то негативная. Оценка тональности создается с помощью методов классификации. Входные признаки, поступающие в классификатор, включают n-граммы, признаки, созданные на основе частеречной разметки, и векторное представление слов.
 
## Извлечение ключевой фразы##
API возвращает список строк с обозначением ключевых тем разговора во входном тексте. Мы используем методы из высокотехнологичного набора средств для обработки естественных языков Microsoft Office.

## Определение интерфейса API##

###GetSentiment###

**URL-адрес**	

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Пример запроса**

В приведенном ниже вызове GET запрашивается тональность фразы *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Получить ключ учетной записи можно [здесь]( https://datamarket.azure.com/account/keys). 

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

В приведенном ниже вызове GET запрашивается тональность ключевых фраз в тексте *Замечательный отель для проживания, с уникальным декором и доброжелательным персоналом.*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Заголовки:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Получить ключ учетной записи можно [здесь]( https://datamarket.azure.com/account/keys). 


**Пример ответа**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->
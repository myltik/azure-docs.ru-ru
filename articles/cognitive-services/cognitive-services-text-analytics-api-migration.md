<properties
	pageTitle="Обновление до версии 2 API анализа текста | Microsoft Azure"
	description="Анализ текста машинного обучения Azure — обновление до версии 2"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2016"
	ms.author="onewth"/>

# Обновление до версии 2 API анализа текста #

Это руководство поможет выполнить обновление кода, в котором используется [первая версия API](../machine-learning-apps-text-analytics/), до второй версии.

Если вы не использовали API и хотите узнать больше, можете **[получить дополнительные сведения об API](//go.microsoft.com/fwlink/?LinkID=759711)** или **[последовать краткому руководству](//go.microsoft.com/fwlink/?LinkID=760860)**. Технический справочник см. в разделе **[Определение API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### Часть 1. Получение нового ключа ###

Во-первых, необходимо получить новый ключ API из **портале Azure**:

1. Перейдите к службе анализа текста с помощью [Коллекции Cortana Intelligence](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Здесь вы также найдете ссылки на документацию и примеры кода.

1. Щелкните **Регистрация**. Откроется портал управления Azure, на котором вы сможете зарегистрироваться для использования службы.

1. Выберите план. Вы можете выбрать **уровень "Бесплатный" на 5 000 транзакций в месяц**. Это бесплатный план, поэтому вы не будете платить за использование службы. Вам понадобится войти в подписку Azure.

1. После регистрации для службы анализа текста вы получите **ключ API**. Скопируйте этот ключ, так как он потребуется вам при использовании служб API.

### Часть 2. Обновление заголовков ###

Обновите значения отправленного заголовка, как показано ниже. Обратите внимание, что ключ учетной записи больше не кодируется.

**Версия 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Версия 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### Часть 3. Обновление базового URL-адреса ###

**Версия 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Версия 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### Часть 4a. Обновление форматов для мнений, ключевых фраз и языков ###

#### Конечные точки ####

Использование метода GET с конечными точками теперь является устаревшим. Поэтому все входные данные необходимо отправлять в запросе POST. Обновите конечные точки в соответствии с приведенными ниже.

| |Версия 1, одиночная конечная точка|Версия 1, пакетная конечная точка|Версия 2, конечная точка|
|---|---|---|---|
|Тип вызова|ПОЛУЧЕНИЕ|ПУБЛИКАЦИЯ|ПУБЛИКАЦИЯ|
|Мнение|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Ключевые фразы|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Языки|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### Форматы входных данных ####

Обратите внимание, что теперь можно использовать только формат POST, поэтому следует соответствующим образом изменить формат всех входных данных, в которых ранее использовались конечные точки для отдельных документов. Регистр входных данных не учитывается.

**Версия 1 (пакетная)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Версия 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Вывод мнения ####

**Версия 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Версия 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Вывод ключевых фраз ####

**Версия 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Версия 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### Вывод языков ####


**Версия 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Версия 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### Часть 4b. Обновление форматов разделов ###

#### Конечные точки ####

| |Версия 1, конечная точка | Версия 2, конечная точка|
|---|---|---|
|Отправка для определения раздела (POST)|```StartTopicDetection```|```topics```|
|Получение результатов для раздела (GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### Форматы входных данных ####

**Версия 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Версия 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### Отправка результатов ####

**Версия 1 (POST)**

Ранее по завершении задания вы получали следующие выходные данные JSON, в которых jobId добавлялся к URL-адресу для получения выходных данных.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Версия 2 (POST)**

Сейчас ответ содержит следующее значение заголовка, в котором `operation-location` используется в качестве конечной точки для опроса результатов:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### Результаты операции ####

**Версия 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Версия 2 (GET)**

Как и прежде, **периодически опрашивайте выходные данные** (предложенный интервал опроса — одна минута) до тех пор, пока они не будут возвращены.

Когда API разделов завершит работу, будет возвращено состояние `succeeded`. Затем будут возвращены выходные результаты в формате, показанном ниже:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### Часть 5. Проверьте, как все работает! ###

Теперь все готово для проверки! Проверьте свой код с небольшим примером, чтобы убедиться, что он успешно обрабатывает данные.

<!---HONumber=AcomDC_0427_2016-->
<properties 
	pageTitle="Настройка конечных точек машинного обучения Azure в Stream Analytics | Microsoft Azure" 
	description="Определяемые пользователем функции машинного обучения в Stream Analytics."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"
/>

# Интеграция машинного обучения в Stream Analytics

Stream Analytics поддерживает определяемые пользователем функции, которые обращаются к конечным точкам машинного обучения Azure. Как эта возможность поддерживается в REST API, см. в статье [Библиотека REST API Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). В этой статье приведены дополнительные сведения, необходимые для успешной реализации этой возможности в Stream Analytics. Опубликованное руководство доступно [здесь](stream-analytics-machine-learning-integration-tutorial.md).

## Обзор терминологии машинного обучения Azure

Машинное обучение Microsoft Azure — это инструмент для совместной работы с поддержкой функции перетаскивания объектов. Он предназначен для создания, тестирования и развертывания решений для прогнозной аналитики данных. Этот инструмент называется *Студия машинного обучения Azure*. Студия будет использоваться для взаимодействия с ресурсами машинного обучения, а также для простого построения и тестирования макета и для выполнения итераций в нем. Эти ресурсы и их определения приведены ниже.

- **Рабочая область**. *Рабочая область* — это контейнер, содержащий все ресурсы машинного обучения для управления и контроля.
- **Эксперимент**. *Эксперименты* создаются специалистами по обработке и анализу данных для использования наборов данных и обучения моделей машинного обучения.
- **Конечная точка**. *Конечные точки* — это объекты машинного обучения Azure, которые используются для принятия функций в качестве входных данных, применения выбранной модели машинного обучения и возвращения результата оценки.
- **Оценивающая веб-служба**. *Оценивающая веб-служба* — это коллекция конечных точек, упомянутых выше.

Каждая конечная точка имеет API для пакетного и синхронного выполнения. В Stream Analytics используется синхронное выполнение. В Студии машинного обучения Azure соответствующая служба называется [службой обработки запросов и ответов](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## Ресурсы машинного обучения, необходимые для заданий Stream Analytics

Для успешной обработки задания Stream Analytics требуется конечная точка «запрос — ответ», [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key) и определение swagger. В Stream Analytics есть дополнительная конечная точка, которая создает URL-адрес конечной точки swagger, ищет интерфейс, а затем возвращает пользователю определение определяемой пользователем функции по умолчанию.

## Настройка Stream Analytics и определяемой пользователем функции машинного обучения с помощью REST API

С помощью интерфейсов REST API можно настроить задание для вызова функций машинного обучения Azure. Для этого необходимо выполнить следующие шаги:

1. Создание задания Stream Analytics
2. Определение входных данных.
3. Определение выходных данных.
4. Создание определяемой пользователем функции.
5. Создание преобразования Stream Analytics, которое вызывает определяемую пользователем функцию.
6. Запустите задание

## Создание определяемой пользователем функции с базовыми свойствами

В следующем примере кода создается скалярная определяемая пользователем функция с именем *newudf*, которая выполняет привязку к конечной точке машинного обучения Azure. Обратите внимание, что *конечную точку* (URI службы) можно найти на странице справки по API для выбранной службы, а *apiKey* — на главной странице служб.

PUT : /subscriptions/<ИД\_подписки>/resourceGroups/<группа\_ресурсов>/providers/Microsoft.StreamAnalytics/streamingjobs/<имя\_задания\_потоковой\_передачи>/functions/<имя\_UDF>?api-version=<версия\_API>

Пример тела запроса:

	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
						"apiKey": "replacekeyhere"
					}
				}
			}
		}
	}

## Вызов конечной точки RetrieveDefaultDefinition для определяемой пользователем функции по умолчанию

После создания схемы определяемой пользователем функции требуется получить ее полное определение. Конечная точка RetreiveDefaultDefinition помогает получить определение по умолчанию для скалярной функции, которая привязана к конечной точке машинного обучения Azure. Для приведенных ниже полезных данных требуется получить определение указанной пользователем функции по умолчанию для скалярной функции, которая привязана к конечной точке машинного обучения Azure. Фактическая конечная точка не указывается, так как она задана во время запроса PUT. Stream Analytics вызовет конечную точку, указанную в запросе, если она задана явно. В противном случае будет использоваться конечная точка, на которую имелась изначальная ссылка. Здесь определяемая пользователем функция принимает один строковой параметр (предложение) и возвращает один вывод строкового типа, который указывает на метку «мнений» для этого предложения.

POST : /subscriptions/<ИД\_подписки>/resourceGroups/<группа\_ресурсов>/providers/Microsoft.StreamAnalytics/streamingjobs/<имя\_задания\_потоковой\_передачи>/functions/<имя\_UDF>/RetrieveDefaultDefinition?api-version=<версия\_API>

Пример тела запроса:

	{
		"bindingType": "Microsoft.MachineLearning/WebService",
		"bindingRetrievalProperties": {
			"executeEndpoint": null,
			"udfType": "Scalar"
		}
	}

Вывод будет выглядеть примерно так, как показано ниже.


	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"inputs": [{
					"dataType": "nvarchar(max)",
					"isConfigurationParameter": null
				}],
				"output": {
					"dataType": "nvarchar(max)"
				},
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
						"apiKey": null,
						"inputs": {
							"name": "input1",
							"columnNames": [{
								"name": "tweet",
								"dataType": "string",
								"mapTo": 0
							}]
						},
						"outputs": [{
							"name": "Sentiment",
							"dataType": "string"
						}],
						"batchSize": 10
					}
				}
			}
		}
	}

## Исправление для определяемой пользователем функции с помощью ответа 

Теперь необходимо исправить определяемую пользователем функцию с использованием предыдущего ответа, как показано ниже.

PATCH : /subscriptions/<ИД\_подписки>/resourceGroups/<группа\_ресурсов>/providers/Microsoft.StreamAnalytics/streamingjobs/<имя\_задания\_потоковой\_передачи>/functions/<имя\_UDF>?api-version=<версия\_API>

Текст запроса: вывод из RetrieveDefaultDefinition

## Реализация преобразования Stream Analytics для вызова определяемой пользователем функции

Теперь выполните запрос к определяемой пользователем функции (здесь с именем scoreTweet) для каждого входного события и запишите ответ для этого события в вывод.

	{
		"name": "transformation",
		"properties": {
			"streamingUnits": null,
			"query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
		}
	}

Дополнительную информацию см. в следующих статьях:

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->

<properties
	pageTitle="Переход с API рекомендаций DataMarket на API рекомендаций Azure Cognitive Services| Microsoft Azure"
	description="Рекомендации по машинному обучению Azure: переход на Cognitive Services API рекомендаций"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="luisca"/>


# Переход с API рекомендаций DataMarket на API рекомендаций Azure Cognitive Services
В этой статье показано, как перейти с [API рекомендаций Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) на [API рекомендаций Microsoft Azure Cognitive Services](https://www.microsoft.com/cognitive-services/ru-RU/recommendations-api).

Прием новых клиентов в API рекомендаций DataMarket будет приостановлен 31 декабря 2016 г., а 28 февраля 2017 г. этот интерфейс станет устаревшим.

## Как начать использовать API рекомендаций Azure Cognitive Services?

Чтобы перейти на API рекомендаций Cognitive Services, сделайте следующее:

1.	Если у вас еще нет подписки Azure, [зарегистрируйтесь](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) для ее получения.

1.	Ознакомьтесь с [кратким руководством](cognitive-services-recommendations-quick-start.md), содержащим пошаговые инструкции по регистрации для перехода на API рекомендаций Cognitive Services и программному использованию этого интерфейса.

1.	Чтобы получить дополнительные сведения о службе, перейдите на [целевую страницу API рекомендаций Cognitive Services](https://www.microsoft.com/cognitive-services/ru-RU/recommendations-api).

## Я использовал пользовательский интерфейс рекомендаций для создания моделей. Предусмотрен ли такой инструмент для API рекомендаций Cognitive Services?

Конечно. Новый [пользовательский интерфейс рекомендаций](http://recommendations-portal.azurewebsites.net/) доступен по URL-адресу http://recommendations-portal.azurewebsites.net.

>[AZURE.NOTE] Учетные данные DataMarket здесь не работают. Зарегистрируйте подписку на портале Azure и получите ключ учетной записи, необходимый для использования нового [пользовательского интерфейса рекомендаций](http://recommendations-portal.azurewebsites.net/). При отсутствии ключа учетной записи см. задачу 1 в [кратком руководстве](cognitive-services-recommendations-quick-start.md).

##Формат нового API и API рекомендаций DataMarket совпадают?

API поддерживает такие же сценарии и потоки процессов, что и версия DataMarket, но фактический API-интерфейс обновлен для соответствия [рекомендациям по REST API Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). API-интерфейсы более согласованы и работают лучше при использовании инструментов, поддерживающих Swagger.

Чтобы ознакомиться с каждым API-интерфейсом, просмотрите [обозреватель API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db). Чтобы испытать вызов API, нажмите кнопку *Попробуйте!*. Формат файлов, используемый в API рекомендаций (каталог и файлы использования), не изменился, поэтому для создания этих файлов можно использовать те же файлы и инфраструктуру.

##Какие новые функции представлены в API рекомендаций Cognitive Services?

В течение последних двух месяцев мы выпустили несколько новых возможностей API рекомендаций Cognitive Services:
-	пользовательский интерфейс рекомендаций для обучения и тестирования моделей без необходимости написания кода;
-	пакетная оценка для предоставления тысяч рекомендаций за один раз;
-	поддержка метрик сборки для запроса на качество моделей рекомендаций;
-	поддержка бизнес-правил;
-	возможность перечисления и скачивания файлов каталога и файлов использования;
-	поддержка сборки ранжирования для запроса на качество признаков элементов в модели рекомендаций;
-	возможность поиска продукта в каталоге.

## Когда будет прекращена поддержка API рекомендаций DataMarket?

Прием новых клиентов в [API рекомендаций DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) будет приостановлен после 31 декабря 2016 г. Начиная с 28 февраля 2017 г. этот интерфейс будет считаться полностью устаревшим.

## Что делать при отсутствии данных, необходимых для повторного создания моделей в API рекомендаций Cognitive Services?

Мы хотим максимально упростить этот переход для вас. Мы можем помочь вам переместить старые модели из учетной записи DataMarket в новую подписку API рекомендаций Azure Cognitive Services. Свяжитесь с нами по адресу [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Чтобы связать ваши модели с новой учетной записью, нам понадобятся ваши идентификаторы подписки DataMarket и Cognitive Services.

<!---HONumber=AcomDC_0907_2016-->
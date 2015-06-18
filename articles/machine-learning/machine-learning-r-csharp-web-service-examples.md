<properties 
	pageTitle="Примеры веб-служб, построенных с помощью языка R в Azure ML и опубликованных в Магазине | Azure" 
	description="Примеры веб-служб, построенных с помощью языка R в Azure ML и опубликованных в Магазине" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Примеры веб-служб, построенных с помощью языка R в Azure ML и опубликованных в Магазине




С помощью Azure Machine Learning Studio пользователи могут написать код R и всего несколькими нажатиями кнопки опубликовать его как веб-службу, чтобы другие пользователи и устройства по всему миру могли ею пользоваться. Благодаря простым вычислениям, предоставляющим статистические функции для создания, например, средства прогнозирования мнений на основе интеллектуального анализа текстов, как новички, так и опытные пользователи кода R почувствуют, как легко пользователи Azure ML могут ввести его в действие. Хотя эти веб-службы могут применяться пользователями (потенциально через мобильное приложение или веб-сайт), они также служат примером того, как Azure ML может вводить в действие скрипты R для аналитических целей и использоваться для создания веб-служб на основе кода R.

На этой странице можно найти множество примеров веб-служб, созданных с помощью Azure ML и опубликованных в Магазине Microsoft Azure. К каждой веб-службе прилагается подробный документ, включающий образцы наборов данных для тестирования служб и объясняющий, как пользователь может самостоятельно создать аналогичную службу. 

![Workflow][1]

Рассмотрим следующие сценарии.

####Сценарий 1. Универсальная модель 
Пользователь работает с универсальной моделью, которая может быть применена к новым пользовательским данным, например с базовым прогнозированием данных временных рядов или с пользовательским методом R с расширенной аналитикой. Этот пользователь публикует модель как веб-службу, чтобы другие пользователи могли использовать ее со своими данными.

* [Бинарный классификатор](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Кластерная модель](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Многомерная линейная регрессия](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Прогнозирование на основе метода экспоненциального сглаживания](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [Прогнозирование ETS+STL](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [Прогнозирование на основе метода авторегрессионного интегрированного скользящего среднего](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-arima/)
* [Анализ выживаемости](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Сценарий 2. Обученная модель - определенные данные 
У пользователя есть данные, которые предоставляют полезные прогнозы через код R, например большая выборка личностных анкет, кластеризованных с помощью алгоритма K-средних для прогнозирования типа личности пользователя, или данные исследования в области здравоохранения, которые могут использоваться для прогнозирования риска заболевания раком легких с помощью пакета R анализа выживаемости. Пользователь публикует данные через веб-службу, которая прогнозирует результат для нового пользователя.

####Сценарий 3. Обученная модель - универсальные данные 
У пользователя есть универсальные данные (например, текстовая база данных), позволяющие построить веб-службу, которую можно применять универсально для различных типов сценариев.

* [Анализ мнений на основе словаря](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Сценарий 4. Расширенный калькулятор 
Пользователь предоставляет комплексные вычисления или моделирования, для которых не требуется обученная модель или подгонка модели под данные пользователя.

* [Тест на разницу в пропорциях](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Набор нормального распределения](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Набор биномиального распределения](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Магазине можно [здесь](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png



<!--HONumber=46--> 
 
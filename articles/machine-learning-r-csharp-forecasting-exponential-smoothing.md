<properties title="Forecasting-Exponential Smoothing" pageTitle="Экспоненциальное сглаживание прогноза | Azure" description="Web service: Forecasting-Exponential Smoothing" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Экспоненциальное сглаживание прогноза 
  



Эта [веб-служба]( https://datamarket.azure.com/dataset/aml_labs/ets) использует модель экспоненциального сглаживания (ETS) для формирования прогнозов на основе предоставленных пользователем исторических данных. Например, она позволяет ответить на такие вопросы, как увеличится ли спрос на определенный товар в этом году или можно ли спрогнозировать объемы продаж на Рождество, чтобы лучше спланировать поставки. Эта модель обнаруживает скрытые и сезонные закономерности в имеющихся данных и на их основе прогнозирует изменения.  
 
>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.
 
##Использование веб-службы 
 
Эта служба принимает 4 аргумента и рассчитывает прогнозы ETS.
Ниже описаны входные аргументы.

* Frequency: обозначает частоту необработанных данных (ежедневные/еженедельные/ежемесячные/ежеквартальные/ежегодные)
* Horizon: горизонт прогнозирования
* Date: новый временной ряд данных со значениями времени
* Value: значения нового временного ряда данных

На выходе служба выдает рассчитанные прогнозируемые значения.

Ниже приведен пример входных данных. 

* Frequency:12
* Horizon: 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложения приведен [здесь](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)).

###Код C# для использования веб-службы:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Создание веб-службы 

>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.

В системе Azure ML был создан новый пустой эксперимент. С помощью стандартной схемы данных были загружены входные значения Со схемой данных связан модуль выполнения сценария на языке R, который создает модель прогнозирования ARIMA с использованим функций ets и forecast из кода R. 


![Experiment flow][2]

####Модуль 1:
 
	# Добавление CSV-файла с данными в приведенном ниже формате 
![Data sample][3]	

####Модуль 2:

	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- ets(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

 
##Ограничения 

Это очень простой пример использования модели прогнозирования ETS. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, служба предполагает, что все переменные являются непрерывными/положительными значениями, а частота должна быть целым числом больше 1. Длины векторов дат и значений должны совпадать Переменные дат должны быть представлены в формате "дд.мм.гггг".

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png

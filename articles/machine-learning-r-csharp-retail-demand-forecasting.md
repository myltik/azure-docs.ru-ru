<properties title="Forecasting - ETS + STL " pageTitle="Прогнозирование ETS+STL  | Azure" description="Прогнозирование ETS+STL " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 

#Прогнозирование ETS+STL  
 
 



Эта [веб-служба]( https://datamarket.azure.com/dataset/aml_labs/demand_forecast) реализует сезонную декомпозицию (STL) и модель экспоненциального сглаживания (ETS) для прогнозирования на основе исторических данных, предоставленных пользователем. Увеличится ли в этом году спрос на определенный продукт? Могу ли я предсказать объем продаж в сезон Рождества, чтобы эффективно спланировать товарные запасы? Модели прогнозирования позволяют решать такие задачи. Используя прошлые данные в качестве базы, эти модели анализируют скрытые тенденции и сезонные колебания для прогнозирования будущих тенденций.  
 
>Хотя эта веб-служба может применяться пользователями (потенциально через мобильное приложение, веб-сайт или даже на локальном компьютере), она также служит примером использования Azure ML для создания веб-служб на основе кода R. С помощью всего нескольких строк кода R и нажатий кнопки в Azure ML Studio можно создать эксперимент с кодом R и опубликовать его как веб-службу. Затем веб-службу можно опубликовать в Магазине Azure, и она может применяться пользователями и устройствами по всему миру без необходимости настройки инфраструктуры автором веб-службы.  
 
##Использование веб-службы 

Эта служба принимает 4 аргумента и рассчитывает прогнозы.
Входные аргументы:

* Частота: указывает частоту необработанных данных (ежедневно, еженедельно, ежемесячно, ежеквартально или ежегодно)
* Период: временные рамки будущего прогноза
* Дата: добавление новых данных временных рядов для времени
* Значение: добавление новых значений данных временных рядов

Результатом выполнения службы являются рассчитанные значения прогноза.
 
Пример вводимых данных: 

* Частота: 12
* Период: 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Значение: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

>Эта служба, размещенная в Магазине Microsoft Azure, является службой на основе OData. Вызвать ее можно методами POST и GET. 

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx )).

###Начало кода C# для использования веб-службы:

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

>Эта веб-служба была создана с помощью Azure ML. Для получения бесплатной пробной версии и вводных видеоматериалов по созданию экспериментов и [публикации веб-служб](http://azure.microsoft.com/ru-ru/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.

В Azure ML был создан новый пустой эксперимент. Был загружен образец входных данных с предопределенной схемой данных. Со схемой данных связан модуль "Выполнение скрипта R", который формирует модель прогнозирования на основе сезонной декомпозиции (STL) и экспоненциального сглаживания (ETS) с помощью функций "stl", "ets" и "forecast" из языка R. 

###Ход эксперимента:

![Experiment flow][2]

####Модуль 1:
 
	# Добавьте в CSV-файл данные в формате, показанном ниже. 
![Sample data][3]	

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
	fit1 <- stl(train_ts,  s.window="periodic")
	train_model <- forecast(fit1, h = data$horizon, method = 'ets')
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

##Ограничения 

Это очень простой пример прогнозирования ETS+STL. Как можно увидеть из приведенного выше примера кода, перехват ошибок не происходит и служба предполагает, что все переменные являются непрерывными/положительными значениями и частота должна быть положительным целым числом больше 1. Длина векторов даты и времени должна совпадать, а длина временного ряда должна быть больше произведения "2*частота". Переменная даты должна быть в формате "мм/дд/гггг".

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Магазине можно [здесь](http://azure.microsoft.com/ru-ru/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png

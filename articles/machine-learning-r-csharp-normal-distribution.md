<properties 
	pageTitle="Набор веб-служб нормального распределения | Azure" 
	description="Набор веб-служб нормального распределения" 
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

#Набор веб-служб нормального распределения



Набор веб-служб нормального распределения состоит из трех веб-служб ([генератор]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [калькулятор квантилей]( https://datamarket.azure.com/dataset/aml_labs/ndq5) и [калькулятор вероятностей]( https://datamarket.azure.com/dataset/aml_labs/ndp5)), предназначенных для создания нормальных распределений и работы с ними. С их помощью можно создать нормальное распределение любой длины, рассчитать квантили для любой заданной вероятности, а также вероятность - для заданного квантиля.  Каждая служба выдает собственный набор выходных данных (см. описание ниже). В основе набора веб-служб нормального распределения лежит использование функций qnorm, rnorm и pnorm, которые входят в пакет статистических функций на языке R.

>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.  
 

##Использование веб-службы
В состав набора веб-служб нормального распределения входят перечисленные ниже три службы.

###Калькулятор квантилей нормального распределения
Эта служба принимает четыре аргумента нормального распределения и рассчитывает соответствующий квантиль.

Ниже описаны входные аргументы.

* p - одна вероятность события с нормальным распределением 
* Mean - среднее значение нормального распределения
* SD - стандартное отклонение нормального распределения 
* Side - L для нижней или U для верхней части распределения

На выходе служба выдает рассчитанный квантиль, связанный с заданной вероятностью.

###Калькулятор вероятности нормального распределения
Эта служба принимает четыре аргумента нормального распределения и рассчитывает соответствующую вероятность.

Ниже описаны входные аргументы.

* q - один квантиль события с нормальным распределением 
* Mean - среднее значение нормального распределения
* SD - стандартное отклонение нормального распределения 
* Side - L для нижней или U для верхней части распределения

На выходе служба выдает рассчитанную вероятность, связанную с заданным квантилем.

###Генератор нормального распределения
Эта служба принимает три аргумента нормального распределения и создает случайную последовательность нормально распределенных чисел. 
Ниже описаны аргументы, передаваемые этой службе в запросе.

* n - количество наблюдений 
* mean - среднее значение нормального распределения
* sd - стандартное отклонение нормального распределения 

На выходе служба выдает последовательность чисел длины n с нормальным распределением на основе аргументов mean и sd.

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложений: [генератор](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx),
[калькулятор вероятностей](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx),
[калькулятор квантилей](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

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
>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). 

Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.

###Калькулятор квантилей нормального распределения
Ход эксперимента
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Калькулятор вероятности нормального распределения
Ход эксперимента
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Генератор нормального распределения
Ход эксперимента
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Ограничения 

Это очень простой пример, выполняющий операции с нормальными распределениями. Как видно из приведенного выше образца кода, в нем практически не отслеживаются ошибки.

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png

<!--HONumber=46--> 

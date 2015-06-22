<properties 
	pageTitle="Набор веб-служб биномиального распределения | Azure" 
	description="Набор биномиального распределения" 
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


#Набор веб-служб биномиального распределения




Набор веб-служб биномиального распределения состоит из трех веб-служб ([биномиальный генератор](https://datamarket.azure.com/dataset/aml_labs/bdg5), [калькулятор вероятностей]( https://datamarket.azure.com/dataset/aml_labs/bdp4) и [калькулятор квантилей]( https://datamarket.azure.com/dataset/aml_labs/bdq5)), предназначенных для создания биномиальных распределений и работы с ними. С их помощью можно создать биномиальное распределение любой длины, рассчитать квантили для любой заданной вероятности, а также вероятность - для заданного квантиля.  Каждая служба выдает собственный набор выходных данных (см. описание ниже). В основе набора веб-служб биномиального распределения лежит использование функций qbinom, rbinom и pbinom, которые входят в пакет статистических функций на языке R. 

>Хотя эти веб-службы можно предложить пользователям (например, непосредственно в каталоге Marketplace, через мобильное приложение, веб-сайт или даже с локального компьютера), их основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.

##Использование веб-службы
В состав набора веб-служб биномиального распределения входят перечисленные ниже три службы.

###Калькулятор квантилей биномиального распределения
Эта служба принимает четыре аргумента нормального распределения и рассчитывает соответствующий квантиль.
Ниже описаны входные аргументы.

- p - единое обобщенное значение вероятности для набора испытаний  
- size - количество испытаний
- prob - вероятность успешного испытания
- Side - L для нижней или U для верхней части распределения 

На выходе служба выдает рассчитанный квантиль, связанный с заданной вероятностью.

###Калькулятор вероятности биномиального распределения
Эта служба принимает четыре аргумента биномиального распределения и рассчитывает соответствующую вероятность.
Ниже описаны входные аргументы.

- q - один квантиль события с биномиальным распределением 
- size - количество испытаний
- prob - вероятность успешного испытания
- side - L для нижней или U для верхней части распределения либо E (одно значение количества успешных испытаний)

На выходе служба выдает рассчитанную вероятность, связанную с заданным квантилем.

###Генератор биномиального распределения
Эта служба принимает три аргумента биномиального распределения и создает случайную последовательность биномиально распределенных чисел. 
Ниже описаны аргументы, передаваемые этой службе в запросе.

- n - количество наблюдений 
- size - количество испытаний
- prob - вероятность успеха

На выходе служба выдает последовательность чисел длины n с биномиальным распределением на основе аргументов size и prob.

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложений: [генератор](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx),
[калькулятор вероятностей](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx),
[калькулятор квантилей](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

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


##Создание веб службы 

>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.

###Калькулятор квантилей биномиального распределения

![Create workspace][4]

####Модуль 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

####Модуль 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Калькулятор вероятности биномиального распределения

![Create workspace][5]

####Модуль 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Модуль 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Генератор биномиального распределения

![Create workspace][6]

####Модуль 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Модуль 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Ограничения 
Это очень простой пример, выполняющий операции с биномиальными распределениями. Как видно из приведенного выше образца кода, в нем практически не отслеживаются ошибки.

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png

<!--HONumber=46--> 
 
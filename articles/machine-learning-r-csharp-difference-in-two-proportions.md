<properties title="Difference in Proportions Test" pageTitle="Проверка различия долей | Azure" description="Тест на разницу в пропорциях" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Проверка различия долей




Существует ли между двумя долями статистическая разница? Предположим, что пользователь хочет сравнить два видеоролика и определить, действительно ли у одного из них доля положительных оценок значимо больше, чем у другого. В условиях большой выборки между долями 0,50 и 0,51 может иметь место статистически значимая разница, в то время как небольшой выборки может быть недостаточно для определения того, действительно ли эти доли являются статистически разными. 

Эта [веб-служба]( https://datamarket.azure.com/dataset/aml_labs/prop_test) проверяет гипотезу различия двух долей на основе введенных пользователем данных о количестве успешных испытаний и общем числе испытаний для двух сравниваемых групп. Например, эту веб-службу может вызывать приложение, которое сравнивает видеоролики и на основе полученных ими оценок сообщает пользователю, действительно ли один из них был более положительно оценен зрителями, чем другой.

>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.


##Использование веб-службы

Эта служба принимает 4 аргумента и выполняет проверку гипотезы для долей.

Ниже описаны входные аргументы.

* Successes1: количество успешных испытаний в выборке 1
* Successes2: количество успешных испытаний в выборке 2
* Total1: размер выборки 1
* Total2: размер выборки 2

На выходе служба выдает результат проверки гипотезы, а также статистику хи-квадрат, значение df, значение p, долю в выборках 1 и 2 и границы доверительного интервала.

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложения приведен [здесь](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

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

В системе Azure ML был создан новый пустой эксперимент с двумя модулями "Выполнение сценария на языке R". В первом из них определяется схема данных, а второй использует команду prop.test языка R для проверки гипотезы в отношении двух долей. 


###Ход эксперимента

![Experiment flow][2]


####Модуль 1:

####Schema definition  

	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Модуль 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Ограничения 

Это очень простой пример проверки различия двух долей. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, а служба предполагает, что все переменные являются непрерывными.

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png

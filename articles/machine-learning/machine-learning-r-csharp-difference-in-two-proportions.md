<properties 
	pageTitle="Проверка различия долей | Microsoft Azure" 
	description="Проверка различия долей" 
	services="machine-learning" 
	documentationCenter="" 
	authors="aniedea" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="aniedea"/>


#Проверка различия долей


Существует ли между двумя долями статистическая разница? Предположим, что пользователь хочет сравнить два видеоролика и определить, действительно ли у одного из них доля положительных оценок значимо больше, чем у другого. При большом размере выборки возможна статистически значительная разница между пропорциями 0,50 и 0,51. При малом размере выборки может быть недостаточно данных, чтобы определить, отличаются ли эти пропорции на самом деле.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Эта [веб-служба](https://datamarket.azure.com/dataset/aml_labs/prop_test) проверяет гипотезу различия двух долей на основе введенных пользователем данных о количестве успешных испытаний и общем числе испытаний для двух сравниваемых групп. Например, эту веб-службу может вызывать приложение, которое сравнивает видеоролики и на основе полученных ими оценок сообщает пользователю, действительно ли один из них был более положительно оценен зрителями, чем другой.

>Эту веб-службу можно использовать через мобильное приложение, веб-сайт или на локальном компьютере. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.


##Использование веб-службы

Эта служба принимает 4 аргумента и выполняет проверку гипотезы для долей.

Входные аргументы:

* Successes1 — количество успешных испытаний в выборке 1.
* Successes2 — количество успешных испытаний в выборке 2.
* Total1 — размер выборки 1.
* Total2 — размер выборки 2.

На выходе служба выдает результат проверки гипотезы, а также статистику хи-квадрат, значение df, значение p, долю в выборках 1 и 2 и границы доверительного интервала.

>Эта служба, размещенная в Azure Marketplace, основана на OData. Вызвать ее можно методами POST и GET.

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)).

###Начало кода C# для использования веб-службы:

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


##Создание веб-службы

>Эта веб-служба была создана с помощью системы машинного обучения Azure. Чтобы получить бесплатную пробную версию и вводные видеоматериалы по созданию экспериментов и [публикации веб-служб](machine-learning-publish-a-machine-learning-web-service.md), посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.

В системе машинного обучения Azure был создан новый пустой эксперимент с двумя модулями [Выполнение сценария R][execute-r-script]. В первом из них определяется схема данных, а второй использует команду prop.test языка R для проверки гипотезы в отношении двух долей.


###Ход эксперимента:

![Ход эксперимента][2]


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
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->
<properties title="Multivariate Linear Regression" pageTitle="Многофакторная линейная регрессия | Azure" description="Multivariate Linear Regression" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Многофакторная линейная регрессия   
 

 
Предположим, у вас есть набор данных и вы хотите быстро спрогнозировать значение зависимой переменной y для каждого отдельного значения (i) на основе других независимых переменных. 
Для создания таких прогнозов часто используется статистическая технология линейной регрессии. Здесь каждая зависимая переменная y считается принимающей непрерывные значения.    

Простейший случай применения этого метода - попытка спрогнозировать вес человека (y) на основе его роста (x). В более сложном сценарии может быть доступна дополнительная информация об исследуемом человеке (например, его пол, раса и т. д.), на основе которой требуется определить его вес. Эта [веб-служба]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) применяет модель линейной регрессии к данным и выдает прогнозируемое значение (y) для каждого из входящих в ряд данных наблюдения.

>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.  

#Использование веб-службы  
Эта веб-служба выдает прогнозируемые значения зависимой переменной на основе независимых переменных для всех наблюдений. Она принимает входные данные в виде блока, где строки разделены запятыми, а столбцы - точками с запятой. Веб-служба принимает по одной строке, причем первый столбец должен содержать зависимую переменную. Ниже приведен пример набора данных.

![Sample data][1]

В наблюдениях, в которых нет зависимой переменной, для значения y необходимо указать NA. Вот пример входной строки для такого набора данных: 10;5;2,18;1;6,6;5.3;2.10,7;5;5,22;3;4,12;2;1,NA;3;4. На выходе служба выдает прогнозируемое значение для каждой строки на основе независимых переменных. 

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложения приведен [здесь](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

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


#Создание веб службы  
>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.


В системе Azure ML был создан новый пустой эксперимент, и в рабочую область было помещено два модуля выполнения сценария на языке R. Эта веб-служба выполняет эксперимент машинного обучения Azure на базе сценария R.  Эксперимент состоит из двух частей: определения схемы и учебной модели с оценкой.  Первый модуль определяет структуру ожидаемого входного набора данных, в котором первая переменная является зависимой, а остальные - независимыми. Второй модуль применяет к входным данным стандартную модель линейной регрессии.  
  
![Experiment flow][3]

####Модуль 1:
 
####определение схемы  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Модуль 2:
####моделирование LM   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))
	maml.mapOutputPort("out");  
 
##Ограничения
Это очень простой пример веб-службы многофакторной линейной регрессии. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, а служба предполагает, что все входные данные являются непрерывными переменными (без категоризации), так как принимает только числовые значения. Кроме того, веб-служба способна обработать лишь ограниченный объем данных, так как является службой запроса/ответа, а также в связи с тем фактом, что модель применяется каждый раз при ее вызове. 

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png

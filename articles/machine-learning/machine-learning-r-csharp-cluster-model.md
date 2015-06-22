<properties 
	pageTitle="Кластерная модель | Azure" 
	description="Кластерная модель" 
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
	ms.date="02/12/2015" 
	ms.author="jaymathe"/>  


#Кластерная модель    



Как спрогнозировать поведение групп владельцев кредитных карт, чтобы снизить для их эмитентов риски, связанные с необходимостью списания безнадежных задолженностей?  Как определить группы личных качеств сотрудников, чтобы повысить производительность их труда? Как врачам распределять пациентов по группам исходя из особенностей их заболеваний? В принципе, на все эти вопросы помогает ответить кластерный анализ.    
   
На практике кластерный анализ позволяет разделить массив наблюдений на две или несколько взаимоисключающих неопределенных групп на основе сочетания переменных. Цель этого метода заключается в создании схемы распределения наблюдений (как правило, людей или связанных с ними признаков) по группам, члены которых обладают некоторыми общими характеристиками. Эта [служба](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) использует для распределения произвольных значений по группам метод k-средних - один из распространенных способов кластеризации. Она принимает на всех данные и количество k кластеров и выдает прогнозы относительно попадания каждого из наблюдений в ту или иную группу. 

>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.  

#Использование веб-службы   
Эта веб-служба распределяет значения по k группам и выдает для каждой строки распределение по группам. Она принимает входные данные в виде блока, где строки разделены запятыми, а столбцы - точками с запятой. Веб-служба принимает на вход по одной строке. Ниже приведен пример набора данных.

![Sample data][1]

Предположим, что пользователь хочет разделить эти значения на три взаимоисключающие группы. Вот пример входного блока для такого набора данных: value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". Результатом является прогноз распределения по группам значений в каждой из строк.

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложения приведен [здесь](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

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
>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.

В системе Azure ML был создан новый пустой эксперимент, и в рабочую область было помещено два модуля выполнения сценария на языке R. Схема данных была создана с помощью простого модуля сценария, выполняемого на языке R, а затем связана с разделом кластерной модели, который также был создан на основе такого модуля. В сценарии, выполняемом на языке R, который использовался для создания модели, веб-служба применяет стандартную функцию k-means, которая включена в соответствующий модуль в среде Azure ML.    
   

     
![Experiment flow][3]

####Модуль 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	maml.mapOutputPort("mydata");     
	

####Модуль 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#Ограничения
Это очень простой пример веб-службы кластеризации. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, а служба предполагает, что все входные данные являются непрерывными переменными (без категоризации), так как принимает только числовые значения. Кроме того, веб-служба способна обработать лишь ограниченный объем данных, так как является службой запроса/ответа, а также в связи с тем фактом, что модель применяется каждый раз при ее вызове. 

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png

<!--HONumber=46--> 
 
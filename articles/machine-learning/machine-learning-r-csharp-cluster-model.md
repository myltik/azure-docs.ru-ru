<properties 
	pageTitle="Кластерная модель | Microsoft Azure" 
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
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Кластерная модель    

Как спрогнозировать поведение групп владельцев кредитных карт, чтобы снизить для их эмитентов риски, связанные с необходимостью списания безнадежных задолженностей? Как определить группы личных качеств сотрудников, чтобы повысить производительность их труда? Как врачам распределять пациентов по группам исходя из особенностей их заболеваний? В принципе, на все эти вопросы помогает ответить кластерный анализ.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
   
Кластерный анализ позволяет разделить массив наблюдений на две или несколько взаимоисключающих неопределенных групп на основе сочетания переменных. Цель этого метода заключается в создании схемы распределения наблюдений (как правило, людей или связанных с ними признаков) по группам, члены которых обладают некоторыми общими характеристиками. Эта [служба](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) использует для распределения произвольных значений по группам метод k-средних — один из распространенных способов кластеризации. Она принимает на всех данные и количество k-кластеров и выдает прогнозы относительно попадания каждого из наблюдений в ту или иную k-группу.

>Пользователи могут работать с этой веб-службой через мобильное приложение, веб-сайт или даже локальный компьютер. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.

##Использование веб-службы   
Эта веб-служба распределяет значения по k группам и выдает для каждой строки распределение по группам. Она принимает входные данные в виде блока, где строки разделены запятыми (,), а столбцы — точками с запятой (;). Веб-служба принимает на вход по одной строке. Ниже приведен пример набора данных.

![Пример данных][1]

Предположим, что пользователь хочет разделить эти значения на 3 взаимоисключающие группы. Вот пример входной строки для такого набора данных: value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". Результатом является прогноз распределения по группам значений в каждой из строк.

>Эта служба, размещенная в Azure Marketplace, основана на OData. Вызвать ее можно методами POST и GET.

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)).

###Начало кода C# для использования веб-службы:

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

В системе машинного обучения Azure был создан новый пустой эксперимент, и в рабочую область было помещено два модуля [Выполнение сценария R][execute-r-script]. Схема данных была создана с помощью простого модуля [Выполнение сценария R][execute-r-script]. Затем она была связана с разделом кластерной модели, который также был создан на основе модуля [Выполнение сценария R][execute-r-script]. В модуле [Выполнение сценария R][execute-r-script], который использовался для создания модели, веб-служба применяет стандартную функцию k-means, которая включена в модуль [Выполнение сценария R][execute-r-script] в среде машинного обучения Azure.
   

     
![Ход эксперимента][3]

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

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##Ограничения
Это очень простой пример веб-службы кластеризации. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, а служба предполагает, что все входные данные являются непрерывными переменными (без категоризации), так как принимает только числовые значения. Кроме того, веб-служба способна обработать лишь ограниченный объем данных, так как это служба запроса/ответа, а модель применяется каждый раз при ее вызове.

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=August15_HO6-->
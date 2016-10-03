<properties 
	pageTitle="Двоичный классификатор | Microsoft Azure" 
	description="Двоичный классификатор" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016" 
	ms.author="jaymathe"/>



#Двоичный классификатор

Предположим, у вас есть набор данных и вы хотите спрогнозировать значение двоичной зависимой переменной на основе независимых переменных. Для создания таких прогнозов часто используется статистическая технология логистической регрессии. В этом случае зависимая переменная является двоичной или дихотомической, а p — это вероятность появления интересующей нас характеристики.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Простейшая ситуация применения этой модели — попытка определить, примет ли потенциальный студент приглашение в университет на основе информации о нем (средний балл в аттестате, семейный доход, место проживания, пол). Прогнозируемый результат — это вероятность принятия таким потенциальным учащимся приглашения на учебу. Эта [веб-службы](https://datamarket.azure.com/dataset/aml_labs/log_regression) соответствует модели логистической регрессии для данных и выводит значение вероятности (y) для каждого наблюдения в данных.
  
>Пользователи могут работать с этой веб-службой через мобильное приложение, веб-сайт или даже локальный компьютер. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.
  

##Использование веб-службы  
Эта веб-служба выдает прогнозируемые значения зависимой переменной на основе независимых переменных для всех наблюдений. Она принимает входные данные в виде блока, где строки разделены запятыми (,), а столбцы — точками с запятой ($). Веб-служба принимает по одной строке, причем первый столбец должен содержать зависимую переменную. Ниже приведен пример набора данных.

![Пример данных][1]

В наблюдениях, в которых нет зависимой переменной, для значения y необходимо указать NA. Вот пример входной строки для такого набора данных: "1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4". На выходе служба выдает прогнозируемое значение для каждой строки на основе независимых переменных.

>Эта служба, размещенная в Azure Marketplace, основана на OData. Вызвать ее можно методами POST и GET.

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx)).

###Начало кода C# для использования веб-службы:

	public class Input
	{
   		public string value;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
		byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
		return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
		var input = new Input() { value = TextBox1.Text };
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

В системе машинного обучения Azure был создан новый пустой эксперимент, и в рабочую область было помещено два модуля [Выполнение сценария R][execute-r-script]. Эта веб-служба выполняет эксперимент машинного обучения Azure на базе сценария R. Эксперимент состоит из двух частей: определение схемы и обучение модели + оценка. Первый модуль определяет структуру ожидаемого входного набора данных, в котором первая переменная является зависимой, а остальные — независимыми. Второй модуль применяет к входным данным стандартную модель логистической регрессии.

![Ход эксперимента][2]

####Модуль 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) 
	maml.mapOutputPort("data");  

####Модуль 2:
	#GLM modeling   
	data <- maml.mapInputPort(1) # class: data.frame  
	
	data.split <- strsplit(data[1,1], ",")[[1]] 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	
	model <- glm(data.split$V1 ~., family='binomial', data=data.split)  
	out <- data.frame(predict(model,data.split, type="response")) 
	pred1 <- as.data.frame(out) 
	group <- array(1:nrow(pred1)) 
	for (i in 1:nrow(pred1))  
		{
		if(as.numeric(pred1[i,])>0.5) {group[i]=1} 
		else {group[i]=0}
		} 
	pred2 <- as.data.frame(group) 
	maml.mapOutputPort("pred2");  


##Ограничения
Это очень простой пример веб-службы двоичной классификации. Как видно из приведенного выше образца кода, в нем не отслеживаются ошибки, а служба предполагает, что все входные данные являются двоичными/непрерывными переменными (без категоризации), так как принимает только числовые значения. Кроме того, веб-служба способна обработать лишь ограниченный объем данных, так как это служба запроса/ответа, а модель применяется каждый раз при ее вызове.

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->
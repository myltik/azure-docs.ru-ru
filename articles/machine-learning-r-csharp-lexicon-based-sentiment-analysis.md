<properties title="Lexicon Based Sentiment Analysis" pageTitle="Шаг 1: Лексический анализ мнений | Azure" description="Lexicon Based Sentiment Analysis" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 



#Лексический анализ мнений 

 


Как оценить мнение пользователя и его отношение к определенным фирмам или темам в социальной сети (например, в записях Facebook, твитах, оценках и т. д.)? Для этого существуют методики анализа мнений.

В частности, для анализа мнений используются два общих метода: один на основе алгоритма контролируемого, а второй - неконтролируемого обучения. Алгоритм контролируемого обучения в общем виде создает модель классификации на основе большого объема прокомментированных материалов, и его точность зависит главным образом от качества комментариев, а процесс обучения занимает большое время. Кроме того, при применении этого алгоритма к другой предметной области результат, как правило, оказывается не очень хорошим. В отличие от контролируемого обучения, лексический метод неконтролируемого обучения использует словарь мнений и не требует хранения большого объема данных и обучения, что существенно ускоряет весь процесс. 

Наша [служба](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) создана на основе лексикона MPQA(http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/) - одного из самых распространенных лексиконов субъективных мнений. Он включает 5097 слов для отрицательной оценки и 2533 слова - для положительной. Все они обозначены как имеющие сильную или слабую полярность. Весь массив предоставляется на условиях универсальной общедоступной лицензии GNU. Веб-службу можно применять к любым коротким фразам, таким как твиты, записи в Facebook и т. п. 

>Хотя эту веб-службу можно предложить пользователям (например, через мобильное приложение, веб-сайт или даже с локального компьютера), ее основная цель - продемонстрировать возможности использования платформы Azure ML для создания веб-служб на базе кода R. Имея лишь небольшой объем такого кода, вы можете несколькими нажатиями кнопки в системе Azure ML Studio создать эксперимент и опубликовать его в качестве веб-службы. Авторы таких служб могут размещать их в магазине Azure Marketplace и предлагать пользователям и устройствам со всего мира, не тратя время и усилия на создание соответствующей инфраструктуры.

##Использование веб-службы

Входными данными может быть любой текст, но веб-служба показывает лучшие результаты для коротких фраз. На выходе она выдает числовое значение в диапазоне от -1 до 1. Любое значение меньше 0 означает отрицательный характер текста, больше 0 - положительный. Абсолютное значение результата определяет степень уверенности данного мнения. 

>Эта служба публикуется в каталоге Microsoft Azure Marketplace в качестве службы OData, и для ее вызова можно использовать методы POST и GET. 

Автоматизировать использование этой службы можно несколькими способами (пример приложения приведен [здесь](http://microsoftazuremachinelearning.azurewebsites.net/)).

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


Для входного текста "Сегодня хороший день" служба возвращает 1, указывая на положительное мнение, высказанное в исходной фразе. 

##Создание веб-службы
>Эта веб-служба создана с помощью Azure ML. Инструкции по использованию бесплатной пробной версии, а также ознакомительные видеоматериалы о создании экспериментов и [публикации веб-служб](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) см. на веб-сайте [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, с помощью которого была создана веб-служба, а также пример кода для каждого из его модулей.


В системе Azure ML был создан новый пустой эксперимент. На рисунке ниже показан ход эксперимента лексического анализа мнений. Файл sent_dict.csv - лексикон субъективных мнений MPQA, который передается на вход сценарию на языке R. Еще один входной аргумент - проверяемый образец из набора данных обзоров Amazon, для которого была проведена выборка, изменены названия столбцов и выполнено разделение.  Для хранения в памяти лексикона субъективных мнений и ускорения расчета оценки мы использовали пакет функций хэширования. Пакет tm выполняет разметку текста, после чего он сравнивается со словом в словаре мнений. Итоговая оценка рассчитывается путем применения весовой характеристики каждого слова субъективной оценки в тексте. 

###Ход эксперимента

![experimenmt flow][2]


####Модуль 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }
    # Sample operation
    data.set <- data.frame(result)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Ограничения

С алгоритмической точки зрения лексический анализ мнений - это общий инструмент анализа мнений, показывающий результаты не лучше, чем метод классификации на основе определенных полей. Проблема отрицания не была решена качественным образом. Мы внесли несколько слов отрицания в код программы, однако вместо этого следовало бы использовать словарь отрицаний в сочетании с некоторым набором правил. Веб-служба показывает лучшие результаты на коротких и простых предложениях, таких как твиты и записи в Facebook, чем на длинных и сложных предложениях, таких как обзоры Amazon. 

##Часто задаваемые вопросы
Ответы на часто задаваемые вопросы об использовании веб-служб и их публикации в магазине Marketplace см. [здесь](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png









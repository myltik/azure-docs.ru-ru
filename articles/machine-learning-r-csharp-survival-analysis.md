<properties 
	pageTitle="Анализ выживаемости | Azure" 
	description="Вероятность события анализа выживаемости" 
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

#Анализ выживаемости 





Во многих сценариях основной результат оценки - это время до наступления конкретного события. Другими словами, задается вопрос "Когда произойдет это событие?". В качестве примеров можно привести ситуации, в которых данные описывают время (дни, годы, расстояние и т. д.), прошедшее до наступления интересующего нас события (рецидив болезни, получение степени доктора наук, выход из строя тормозной колодки). Каждый экземпляр данных представляет определенный объект (пациента, человека, автомобиль и т. д.).

Эта [веб-служба]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) отвечает на вопрос "Какова вероятность того, что определенное событие произойдет ко времени n для объекта x?". Предоставляя модель анализа выживаемости, эта веб-служба позволяет пользователям загружать данные для обучения модели и тестировать их. Основная тема эксперимента - моделирование периода времени до наступления определенного события. 

>Хотя эта веб-служба может применяться пользователями (потенциально через мобильное приложение, веб-сайт или даже на локальном компьютере), она также служит примером использования Azure ML для создания веб-служб на основе кода R. С помощью всего нескольких строк кода R и нажатий кнопки в Azure ML Studio можно создать эксперимент с кодом R и опубликовать его как веб-службу. Затем веб-службу можно опубликовать в Магазине Azure, и она может применяться пользователями и устройствами по всему миру без необходимости настройки инфраструктуры автором веб-службы.  

##Использование веб-службы

В следующей таблице показана схема входных данных веб-службы. Требуется шесть блоков входных данных: обучающие данные, тестовые данные, время, индекс параметра "time", индекс параметра "event" и типы переменных (непрерывные или факторные). Обучающие данные представлены последовательностью символов, в которой строки разделяются запятой, а столбцы точкой с запятой. Количество функций данных может меняться. Все элементы во входной строке должны быть числовыми. В обучающих данных параметр "time" указывает количество единиц времени (дни, годы, расстояние и т. д.), прошедших с момента начала исследования (пациент начал программу лечения от наркомании, кандидат начал писать диссертацию для получения степени доктора наук, автомобиль начал эксплуатироваться и т. д.) до наступления определенного события (возврат пациентом к приему наркотиков, получение степени доктора наук, выход из строя тормозной колодки). Параметр "event" указывает, происходит ли определенное событие в конце исследования. "event=1" означает, что определенное событие происходит ко времени, заданному параметром "time", а "event=0" означает, что определенное событие еще не произошло ко времени, заданному параметром "time".

- trainingdata: последовательность символов. Строки разделяются запятой, а столбцы точкой с запятой. Каждая строка включает параметр "time", параметр "event" и прогностические переменные.
- testingdata: одна строка данных, содержащая прогностические переменные для определенного объекта.
- time_of_interest: период времени, n.
- index_time: индекс столбца параметра "time" (начиная с 1).
- index_event: индекс столбца параметра "event" (начиная с 1).
- variable_types: последовательность символов с точкой с запятой в качестве разделителя. 0 представляет непрерывные переменные, а 1 - факторные переменные.


Выходные данные - это вероятность того, что событие произойдет к определенному времени. 

>Эта служба, размещенная в Магазине Microsoft Azure, является службой на основе OData. Вызвать ее можно методами POST и GET. 

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

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


Интерпретация этого теста выглядит следующим образом. Предположим, что цель данных - смоделировать время до возврата к приему наркотиков пациентами, получавшими лечение по одной из двух программ. Выходные данные веб-службы следующие: для пациентов в возрасте 35 лет, дважды проходивших лечение от наркомании, проходящих длительную стационарную реабилитацию и принимавших героин и кокаин, вероятность возврата к приему наркотиков составляет 95,64 % к дню 500.

##Создание веб-службы

>Эта веб-служба была создана с помощью Azure ML. Для получения бесплатной пробной версии и вводных видеоматериалов по созданию экспериментов и [публикации веб-служб](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.

В Azure ML был создан новый пустой эксперимент, и в рабочую область были извлечены два модуля "Выполнение скрипта R". Схема данных была создана с использованием простого модуля "Выполнение скрипта R", который определяет схему входных данных для веб-службы. Затем этот модуль был связан со вторым модулем "Выполнение скрипта R", который отвечает за основные операции. Этот модуль осуществляет предварительную обработку данных, построение модели и прогнозирование. На этапе предварительной обработки данных входные данные, представленные длинной последовательностью символов, преобразуются в блок данных. На этапе построения модели сначала устанавливается внешний пакет R "survival_2.37-7.zip" для проведения анализа выживаемости. Затем после ряда задач по обработке данных выполняется функция "coxph". Подробные сведения о функции "coxph" для анализа выживаемости можно найти в документации по языку R. На шаге прогнозирования тестируемый экземпляр загружается в обученную модель с функцией "surfit", и для данного тестируемого экземпляра создается кривая выживаемости в виде переменной "curve". Наконец, выдается ответ относительно вероятности того, что событие произойдет к указанному времени. 

###Ход эксперимента:

![experiment flow][1]

####Модуль 1:

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Модуль 2:

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Ограничения

Эта веб-служба принимает только числовые значения для переменных функций (столбцов). Столбец "event" может принимать только значение 0 или 1. Столбец "time" должен быть положительным целым числом.

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Магазине можно [здесь](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png

<!--HONumber=46--> 

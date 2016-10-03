<properties 
	pageTitle="Анализ выживаемости в системе машинного обучения Azure | Microsoft Azure" 
	description="Вероятность события анализа выживаемости" 
	services="machine-learning" 
	documentationCenter="" 
	authors="zhangya" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="zhangya"/>


#Анализ выживаемости 

Во многих сценариях основной результат оценки — это время до наступления конкретного события. Другими словами, задается вопрос "когда произойдет это событие?". В качестве примеров можно привести ситуации, в которых данные описывают время (дни, годы, расстояние и т. д.), прошедшее до наступления интересующего нас события (рецидив болезни, получение степени доктора наук, выход из строя тормозной колодки). Каждый экземпляр данных представляет определенный объект (пациента, учащегося, автомобиль и т. д.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Эта [веб-служба](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) отвечает на вопрос "Какова вероятность того, что определенное событие произойдет ко времени n для объекта x?". Предоставляя модель анализа выживаемости, эта веб-служба позволяет пользователям загружать данные для обучения модели и тестировать их. Основная тема эксперимента — моделирование периода времени до наступления определенного события.

>С этой веб-службой могут работать пользователи — через мобильное приложение, веб-сайт или даже локальный компьютер. Веб-служба также служит примером того, как машинное обучение Azure можно использовать для создания веб-служб на основе кода R. Чтобы создать эксперимент с использованием кода R и опубликовать его как веб-службу, достаточно написать несколько строк кода R и нажать несколько кнопок в студии машинного обучения Azure. Затем веб-службу можно опубликовать в Azure Marketplace, и ее смогут применять пользователи и устройства по всему миру, при этом автору веб-службы не придется настраивать инфраструктуру.

##Использование веб-службы

В следующей таблице показана схема входных данных веб-службы. Необходимо шесть фрагментов входных данных: обучающие данные, тестовые данные, время, индекс параметра time, индекс параметра event и типы переменных (непрерывные или факторные). Обучающие данные представлены последовательностью символов, в которой строки разделяются запятой, а столбцы точкой с запятой. Количество функций данных может меняться. Все элементы во входной строке должны быть числовыми. В обучающих данных параметр time указывает количество единиц времени (дни, годы, расстояние и т. д.), прошедших с момента начала исследования (пациент начал программу лечения от наркомании, кандидат начал писать диссертацию для получения степени доктора наук, автомобиль начал эксплуатироваться и т. д.) до наступления определенного события (возврат пациента к приему наркотиков, получение степени доктора наук, выход из строя тормозной колодки). Параметр "event" указывает, происходит ли определенное событие в конце исследования. Значение "event=1" означает, что определенное событие происходит ко времени, заданному параметром time, а "event=0" означает, что определенное событие еще не произошло ко времени, заданному параметром time.

- trainingdata — строка символов. Строки разделяются запятой, а столбцы точкой с запятой. Каждая строка включает параметр "time", параметр "event" и прогностические переменные.
- testingdata — одна строка данных, содержащая прогностические переменные для определенного объекта.
- time\_of\_interest — период времени, n.
- index\_time — индекс столбца параметра time (начиная с 1).
- index\_event — индекс столбца параметра event (начиная с 1).
- variable\_types — строка символов, где разделителем является точка с запятой. 0 представляет непрерывные переменные, а 1 — факторные переменные.


Выходные данные — это вероятность того, что событие произойдет к определенному времени.

>Эта служба, размещенная в Azure Marketplace, является службой на основе OData. Вызвать ее можно методами POST и GET.

Есть несколько способов использования службы в автоматическом режиме (см. пример приложения [здесь](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)).

###Начало кода C# для использования веб-службы:
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




Интерпретация этого теста выглядит следующим образом. Предположим, что цель данных — смоделировать время до возврата к приему наркотиков пациентами, получавшими лечение по одной из двух программ. Выходные данные веб-службы сообщают следующее: для пациентов в возрасте 35 лет, дважды проходивших лечение от наркомании, проходящих длительную стационарную реабилитацию и принимавших героин и кокаин, вероятность возврата к приему наркотиков составляет 95,64 % ко дню 500.

##Создание веб-службы

>Эта веб-служба была создана с помощью системы машинного обучения Azure. Чтобы получить бесплатную пробную версию и вводные видеоматериалы по созданию экспериментов и [публикации веб-служб](machine-learning-publish-a-machine-learning-web-service.md), посетите веб-страницу [azure.com/ml](http://azure.com/ml). Ниже приведен снимок экрана эксперимента, в результате которого была создана веб-служба, и пример кода для каждого модуля в эксперименте.

В системе машинного обучения Azure был создан новый пустой эксперимент, и в рабочую область было помещено два модуля [Выполнение сценария R][execute-r-script]. Схема данных была создана с использованием простого модуля [Выполнение сценария R][execute-r-script], который определяет схему входных данных для веб-службы. Затем этот модуль был связан со вторым модулем [Выполнение сценария R][execute-r-script], который отвечает за основные операции. Этот модуль осуществляет предварительную обработку данных, построение модели и прогнозирование. На этапе предварительной обработки данных входные данные, представленные длинной последовательностью символов, преобразуются в блок данных. На этапе построения модели сначала устанавливается внешний пакет R "survival\_2.37-7.zip" для проведения анализа выживаемости. Затем после ряда задач по обработке данных выполняется функция "coxph". Подробные сведения о функции coxph для анализа выживаемости можно найти в документации по языку R. На шаге прогнозирования тестируемый экземпляр загружается в обученную модель с функцией "surfit", и для данного тестируемого экземпляра создается кривая выживаемости в виде переменной "curve". Наконец, выдается ответ относительно вероятности того, что событие произойдет к указанному времени.

###Ход эксперимента:

![Ход эксперимента][1]

####Модуль 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Модуль 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
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

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
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

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Ограничения

Эта веб-служба принимает только числовые значения для переменных функций (столбцов). Столбец event принимает только значения 0 или 1. Столбец time должен быть положительным целым числом.

##Часто задаваемые вопросы
Ознакомиться с часто задаваемыми вопросами по использованию веб-службы и публикации в Azure Marketplace можно [здесь](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->
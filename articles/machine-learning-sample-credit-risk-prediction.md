<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Пример машинного обучения: прогнозирование кредитных рисков | Azure" description="Пример эксперимента с Машинным обучением Azure, позволяющий разработать модель двоичной классификации, которая прогнозирует, находится ли соискатель в зоне низкого или высокого риска кредитоспособности." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Пример машинного обучения Azure: прогнозирование кредитных рисков 

>[AZURE.NOTE]
>[Пример эксперимента](#sample-experiment) и [образец набора данных](#sample-dataset), связанные с этой моделью, доступны в ML Studio. См. дополнительные сведения ниже.

*Подробное руководство по созданию и использованию упрощенной версии этого эксперимента см. в разделе [Разработка прогнозирующего решения с помощью машинного обучения Azure](http://azure.microsoft.com/ru-ru/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).*

Целью данного эксперимента является прогнозирование кредитного риска на основе информации, представленной в заявлении на кредит. Прогноз имеет двоичное значение: низкий уровень риска или высокий уровень риска. 


<!-- Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment - for experimenting with different models
- Training Experiment - to train the one chosen model
- Scoring Experiment - to set up a web service using the trained model
-->

<!-- Removed because we added a section at the bottom describing the dataset
##Dataset Description

The experiment uses the UCI Statlog (German Credit Card) dataset which can be found here: 
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>. 
Мы используем файл german.data с этого веб-сайта.

Набор данных классифицирует людей, описываемых набором атрибутов, как заемщиков с низким или высоким уровнем риска. Каждый пример представляет собой физическое лицо. Имеется 20 показателей, чисел и категорий, и двоичная метка (уровень кредитного риска). Записи с высоким уровнем кредитного риска имеют метку 2, записи с низким уровнем кредитного риска имеют метку 1. Цена неправильной классификации примера с низким уровнем риска равна 1, в то время как цена неправильной классификации примера с высоким уровнем риска равна 5.
-->

## Эксперимент по разработке

В исходном наборе данных используется формат с разделителями-пробелами. Мы преобразовали набор данных в формат CSV и загрузили его в ML Studio. Преобразование можно провести с использованием Powershell: 

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

Либо используя команду Unix sed:

	sed 's/ /,/g' german.data > german.csv

Сначала мы используем **редактор метаданных** для добавления названий столбцов, чтобы заменить используемые по умолчанию названия столбцов набора данных более понятными, взятыми из описания набора данных на сайте UCI. Новые названия столбцов разделены запятыми в поле **Новое имя столбца** в **редакторе метаданных**.

Затем мы сформируем обучающую и тестовую выборки, которые будут использоваться для разработки модели прогнозирования рисков. Разделим исходный набор данных на обучающую и тестовую выборки одинакового размера с помощью модуля **Разделение** со значением параметра **Доля строк в первой выходной выборке**, равным 0,5.
 
Так как стоимость неправильной классификации примера с высоким уровнем риска в 5 раз выше стоимости неправильной классификации примера с низким уровнем риска, мы сформируем новый набор данных, отображающий эту функцию стоимости. В новом наборе данных каждый пример с высоким уровнем риска повторяется 5 раз, а примеры с низким уровнем риска остаются без изменений. Разделение на обучающую и тестовую выборки производится до этой репликации, чтобы один и тот же пример не попал одновременно в обе выборки. 

Репликация производится с помощью следующего кода R, который выполняется модулем **Выполнение скрипта R**:

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

В нашем эксперименте мы сравниваем два подхода к формированию моделей: обучение с помощью исходного набора данных и обучение с помощью реплицированного набора данных. В обоих подходах для сравнения с функцией стоимости задачи мы будем проверять на тестовой выборке с репликацией. Ниже приведен окончательный вид процесса разделения и репликации. В этом процессе левый выход модуля **Разделение** является обучающей выборкой, а правый выход является тестовой выборкой. Обратите внимание, что обучающая выборка в последующем используется как с модулем **Выполнение скрипта R**, так и без него (то есть с репликацией и без нее).

![Splitting training and test data][screen1]
 
Помимо проверки эффекта репликации примеров в обучающей выборке, мы также сравним характеристики двух алгоритмов: метод опорных векторов (SVM) и увеличивающееся дерево решений. Таким образом мы сформируем 4 модели:

- SVM, обучение на исходных данных;
- SVM, обучение на реплицированных данных;
- увеличивающееся дерево решений, обучение на исходных данных;
- увеличивающееся дерево решений, обучение на реплицированных данных.

Увеличивающиеся деревья решений хорошо работают с атрибутами любого типа. Однако, так как модуль SVM создает линейный классификатор, генерируемая им модель имеет наименьшую ошибку при проверке среди всех атрибутов одного масштаба. Для преобразования всех атрибутов в один масштаб используем модуль **Преобразование данных масштабированием** с преобразованием tanh. При этом все числовые атрибуты приводятся к значению в интервале [0,1]. Обратите внимание, что строковые атрибуты преобразуются модулем SVM в категориальные атрибуты, а затем в бинарные атрибуты 0/1, поэтому вручную их преобразовывать не нужно. 

Инициализируем алгоритм обучения с использованием модуля **Двухклассовый метод опорных векторов** или **Двухклассовое увеличивающееся дерево решений**, а затем используем модуль **Обучение модели** для создания собственно модели. Эти модели используются модулями **Оценка модели** для создания оценок тестовых примеров. Ниже приведен пример процесса, комбинирующего эти модули и использующего SVM и реплицированную обучающую выборку. Обратите внимание, что модуль **Обучение модели** подключен к обучающей выборке, а модуль **Оценка модели** подключен к тестовой выборке.

![Training and scoring a model][screen2]

На стадии оценки эксперимента мы вычислим точность каждой из четырех указанных выше моделей. Для этого используем модуль **Анализ модели**. Обратите внимание, что этот модуль вычисляет точность, только когда все примеры имеют одну и ту же стоимость неправильной классификации. Но, поскольку ранее мы выполнили репликацию положительных примеров, точность, вычисляемая модулем **Анализ модели**, зависит от стоимости и равна 

![Accuracy computation][formula]

где *n+* и *n-* - это количество положительных и отрицательных примеров в исходном наборе данных, а *e+* и *e-* - это количество неправильно классифицированных положительных и отрицательных примеров в исходном наборе данных.

Модуль **Анализ модели** сравнивает 2 модели, поэтому мы используем один модуль **Анализ модели** для сравнения двух моделей SVM и еще один для сравнения двух моделей увеличивающегося дерева решений. Сведем все в таблицу для просмотра всех четырех результатов. **Анализ модели** выводит таблицу с одной строкой, которая содержит различные метрики. Мы используем модуль **Добавление строк** для сведения всех результатов в одну таблицу. Затем добавим в таблицу показатели точности четырех модулей, используя скрипт R в модуле **Выполнение скрипта R**, где вручную введем названия строк окончательной таблицы. Наконец, удалим столбцы с несущественными метриками с помощью модуля **Столбцы проекта**. 

Окончательные результаты эксперимента, которые можно получить, щелкнув правой кнопкой мыши по выходу **Результирующий набор данных** модуля **Столбцы проекта**:

![Results][results] 

где первый столбец - это название алгоритма машинного обучения, используемого для формирования модели, второй столбец - тип обучающей выборки, а третий столбец - точность с учетом стоимости. В данном эксперименте наивысшую точность обеспечивает модель SVM, работающая с реплицированной обучающей выборкой.

<!-- Removed until the Training and Scoring parts are fixed
##Training Experiment

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model. 

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.

![Azure storage parameters][screen3] 

##Scoring Experiment

The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model. 

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**. 

After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.

When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.

![Web service ready for production][screen4] 
-->

## Пример эксперимента

Следующий пример эксперимента, связанный с этой моделью, доступен в ML Studio в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**.

> **Пример эксперимента - German Credit - Разработка**


## Образец набора данных

Следующий образец набора данных, используемый для этого эксперимента, доступен в ML Studio в палитре модулей в разделе **Сохраненные наборы данных**.

### Набор данных German Credit Card UCI
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]



[screen1]:./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[screen2]:./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[formula]:./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[results]:./media/machine-learning-sample-credit-risk-prediction/results.jpg
[screen3]:./media/machine-learning-sample-credit-risk-prediction/screen3.jpg
[screen4]:./media/machine-learning-sample-credit-risk-prediction/screen4.jpg

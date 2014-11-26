<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Machine Learning Sample: Credit risk prediction | Azure" description="A sample Azure Machine Learning experiment to develop a binary classification model that predicts if an applicant is a low credit risk or a high credit risk." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Пример машинного обучения Azure: Прогнозирование кредитных рисков

*Пример эксперимента, связанный с этой моделью в ML Studio, можно найти в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**. Имя эксперимента:*

    Sample Experiment - German Credit - Development

*Подробное руководство по созданию и использованию упрощенной версии этого эксперимента см. в разделе [Разработка прогнозирующего решения с помощью машинного обучения Azure.][Разработка прогнозирующего решения с помощью машинного обучения Azure.].*

Целью данного эксперимента является прогнозирование кредитного риска на основе информации, представленной в заявлении на кредит. Прогноз имеет двоичное значение: низкий уровень риска, либо высокий уровень риска.

<!-- Removed until the Training and Scoring parts are fixed This example is divided into 3 sample experiments:  - Development Experiment – for experimenting with different models - Training Experiment – to train the one chosen model - Scoring Experiment – to set up a web service using the trained model -->

## Описание набора данных

Эксперимент использует набор данных UCI Statlog (немецкие кредитные карты), который можно загрузить по адресу:
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>.
Мы будет использовать файл german.data с этого веб-сайта.

Набор данных классифицирует людей, описываемых набором атрибутов, как заемщиков с низким или высоким уровнем рисков. Каждый пример представляет собой физическое лицо. Имеется 20 показателей, чисел и категорий, и двоичная метка (уровень кредитного риска). Цена неправильной классификации примера с низким уровнем риска равна 1, в то время как цена неправильной классификации примера с высоким уровнем кредитного риска равна 5.

## Эксперимент по разработке

В исходном наборе данных используется формат с разделителями-пробелами. Мы преобразуем набор данные в формат CSV и загрузим его в ML Studio. Преобразование можно провести с использованием Powershell:

    cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

Либо используя команду Unix sed:

    sed 's/ /,/g' german.data > german.csv

Мы начнем с использования **редактора метаданных** для добавления пользовательских названий столбцов и замены названия столбцов набора данных по умолчанию более понятными, которые взяты из описания набора данных с веб-сайта UCI. Новые имена столбцов разделены запятыми в поле **Новое имя столбца** в **редакторе метаданных**.

Затем, создадим обучающую и тестовую выборки, которые будут использоваться для разработки модели прогнозирования рисков. Разделим исходный набор данных на обучающую и тестовую выборки одинакового размера с использованием модуля **Разделение** со значением параметра **Доля строк в первой выходной выборке**, равным 0.5.

Так как стоимость неправильной классификации примера с высоким уровнем риска в 5 раз выше стоимости неправильной классификации примера с низким уровнем риска, мы создадим новый набор данных, отображающий эту функцию стоимости. В новом наборе данных каждый пример с высоким уровнем риска повторяется 5 раз, а примеры с низким уровнем риска остаются без изменений. Разделение на обучающую и тестовую выборки производится до этого копирования, чтобы один и тот же пример не попал в обе выборки одновременно.

Репликация производится с помощью следующего кода на языке R, который выполняется в модуле **Выполнение сценария на языке R**:

    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")

В нашем эксперименте мы сравним два подхода к созданию моделей: обучение по исходной выборке и обучение с реплицированной выборкой. В обоих подходах для сравнения с функцией стоимости задачи мы будем проверять на тестовой выборке с репликацией. Ниже приведен окончательный вид процесса разделения и репликации. В этом процессе левый выход модуля **Разделение** является обучающей выборкой, а правый выход является тестовой выборкой. Обратите внимание, что обучающая выборка в последующем используется как с, так и без модуля **Выполнение сценария на языке R**, т.е. с и без репликации.

![Разделение обучающих и тестовых данных][Разделение обучающих и тестовых данных]

Кроме проверки эффекта репликации примеров в обучающей выборке, мы также сравним характеристики двух алгоритмов: метод опорных векторов (SVM) и "повышенное" дерево принятия решения. Этими методами мы создадим 4 модели:

-   SVM, обучение на исходных данных;
-   SVM, обучение на реплицированных данных;
-   "повышенное" дерево принятия решения, обучение на исходных данных;
-   "повышенное" дерево принятия решения, обучение на реплицированных данных.

"Повышенные" деревья принятия решения хорошо работают с атрибутами любого типа. Однако, так как модуль SVM создает линейный классификатор, генерируемая им модель имеет наименьшую ошибку при проверке среди всех атрибутов одного масштаба. Для преобразования всех атрибутов в один масштаб используем модуль **Преобразование данных масштабированием** с преобразованием tanh. При этом все числовые атрибуты приводятся к значению в интервале [0,1]. Обратите внимание, что строковые атрибуты преобразуются модулем SVM в категорийные атрибуты, а затем в бинарные атрибуты 0/1, поэтому вручную их преобразовывать не нужно.

Инициализируем алгоритм обучения с использованием модуля **Двухклассовый метод опорных векторов** или **Двухклассовое "повышенное" дерево принятия решения**, затем используем модуль **Режим обучения** для создания собственно модели. Эти модели используются модулями **Оценка модели** для создания оценок тестовых примером. Ниже приведен пример процесса, комбинирующего эти модули и использующего SVM и реплицированную обучающую выборку. Обратите внимание, что модуль **Обучение модели** подключен к обучающей выборке, а модуль **Оценка модели** подключен к тестовой выборке.

![Обучение и оценка модели][Обучение и оценка модели]

На стадии оценивания эксперимента мы вычислим точность всех четырех моделей. Для этого используем модуль **Оценивание модели**. Обратите внимание, что это модуль вычисляет точность в случае, когда все примеры имеют одну и ту же стоимость неправильной классификации. Но так как ранее мы сделали репликацию положительных примеров, точность, вычисляемая модулем **Оценивание модели** зависит от стоимости и

![Вычисление точности][Вычисление точности]

где *n+* и *n-* количества положительных и отрицательных примеров в первоначальной выборке, а *e+* и *e-* числа неправильно классифицированных положительных и отрицательных примеров в первоначальной выборке.

Модуль **Оценивание модели** сравнивает 2 модели, поэтому мы используем один модуль **Оценивание модели** для сравнения двух моделей SVM, а второй модуль используем для сравнения двух моделей "повышенного" дерева принятия решения. Сведем все в таблицу для просмотра всех четырех результатов. **Оценивание модели** выводит таблицу с одной строкой, которая содержит различные метрики. Мы используем модуль **Добавление строк** для сведения всех результатов в одну таблицу. Затем добавим в таблицу показатели точности четырех модулей, используя сценарий R в модулей **Выполнение сценария на языке R**, где вручную введем названия столбцов окончательной таблицы. И в итоге удалим столбцы с несущественными метриками с использованием модуля **Столбцы проекта**.

Окончательные результаты эксперимента можно получить, щелкнув правой кнопкой мыши по выходу **Результирующий набор данных** модуля **Столбцы проекта**:

![Результат][Результат]

где первый столбец - имя используемого для генерации модели алгоритма машинного обучения, второй столбец - тип обучающей выборки, третий столбец - точность с учетом стоимости. В данном эксперименте наивысшую точность обеспечила модель SVM, работающая на реплицированной обучающей выборке.

<!-- Removed until the Training and Scoring parts are fixed ##Training Experiment  The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model.   Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.  ![Azure storage parameters][screen3]   ##Scoring Experiment  The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model.   After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**.   After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.  When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.  ![Web service ready for production][screen4]  -->

  [Разработка прогнозирующего решения с помощью машинного обучения Azure.]: http://azure.microsoft.com/ru-ru/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
  [Разделение обучающих и тестовых данных]: ./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
  [Обучение и оценка модели]: ./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
  [Вычисление точности]: ./media/machine-learning-sample-credit-risk-prediction/formula.jpg
  [Результат]: ./media/machine-learning-sample-credit-risk-prediction/results.jpg

---
title: Обработка и анализ данных с использованием специально подготовленной виртуальной машины Linux в Azure | Документация Майкрософт
description: Сведения о том, как выполнить несколько общих задач обработки и анализа данных с использованием специально подготовленной виртуальной машины Linux.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 59d6b960a40910b8b2fe72f6c3b149608ee8b8ad
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798076"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Обработка и анализ данных с помощью виртуальной машины для обработки и анализа данных Linux в Azure
В этом пошаговом руководстве показано, как выполнить несколько общих задач обработки и анализа данных с использованием специально подготовленной виртуальной машины Linux. Виртуальная машина Linux для обработки и анализа данных — это доступный в Azure образ ВМ, на которой предварительно установлен ряд инструментов, обычно используемых для анализа данных и машинного обучения. Основные программные компоненты описаны в статье [Подготовка виртуальной машины Linux для обработки и анализа данных](linux-dsvm-intro.md) . Образ виртуальной машины позволяет за считаные минуты приступить к обработке и анализу данных, не требуя установки и настройки всех инструментов по отдельности. При необходимости виртуальную машину можно с легкостью масштабировать и приостановить, если она не используется. Таким образом, это гибкий и экономичный ресурс.

Рассматриваемые здесь задачи обработки и анализа данных соответствуют шагам, описанным в разделе [Процесс обработки и анализа данных группы](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Этот процесс представляет собой системный подход, позволяющий группам специалистов, работающих с данными, эффективно взаимодействовать друг с другом в течение всего жизненного цикла создания интеллектуальных приложений. Кроме того, этот процесс может служить итеративной платформой для обработки и анализа данных, которую можно использовать самостоятельно.

В этом пошаговом руководстве мы анализируем набор данных [spambase](https://archive.ics.uci.edu/ml/datasets/spambase). Это набор сообщений электронной почты, отмеченных как нежелательная или обычная почта, вместе со статистикой по их содержимому (она рассмотрена в одном из следующих разделов).

## <a name="prerequisites"></a>предварительным требованиям
Перед использованием виртуальной машины Linux для обработки и анализа данных необходимо убедиться в наличии следующих компонентов:

* **Подписка Azure**. Если у вас ее нет, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
* [**Виртуальная машина Linux для обработки и анализа данных.**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm) Сведения о подготовке этой ВМ приведены в статье [Подготовка виртуальной машины Linux для обработки и анализа данных](linux-dsvm-intro.md).
* Клиент [X2Go](http://wiki.x2go.org/doku.php), установленный на компьютере, и открытый сеанс XFCE. Сведения об установке и настройке **клиента X2Go** см. в разделе [Установка и настройка клиента X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Чтобы прокрутка лучше работала, переключите флаг gfx.xrender.enabled в положение about:config в обозревателе FireFox виртуальных машин. [Дополнительные сведения](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Также попробуйте установить для фильтра *mousewheel.enable_pixel_scrolling* значение False. [Инструкции см. здесь.](https://support.mozilla.org/en-US/questions/981140)
* **Учетная запись машинного обучения Azure**. Если у вас ее нет, зарегистрируйте учетную запись на [домашней странице Машинного обучения Azure](https://studio.azureml.net/). Для новичков предусмотрен период бесплатного использования.

## <a name="download-the-spambase-dataset"></a>Скачивание набора данных spambase
Набор данных [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) относительно небольшой. Он содержит всего 4601 пример. Это подходящий размер, чтобы продемонстрировать некоторые основные функции ВМ для обработки и анализа данных при небольших требованиях к ресурсам.

> [!NOTE]
> В этом пошаговом руководстве используется виртуальная машина Linux размера D2 v2. Этот размер подходит для выполнения описанных здесь процедур.
>
>

Если требуется больше места для хранения, можно создать дополнительные диски и присоединить их к виртуальной машине. Эти диски используют постоянное хранилище Azure, поэтому данные сохраняются, даже если сервер повторно подготовлен из-за изменения размера или завершения работы. Чтобы добавить диск и присоединить его к виртуальной машине, следуйте инструкциям в [этой статье](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В этих инструкциях используется интерфейс командной строки Azure (Azure CLI), который уже установлен на рассматриваемой виртуальной машине. Поэтому эти процедуры можно выполнить прямо из нее. Чтобы увеличить ресурсы хранения, можно также использовать [файлы Azure](../../storage/files/storage-how-to-use-files-linux.md).

Чтобы скачать данные, откройте окно терминала и выполните следующую команду:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Скачанный файл не содержит строку заголовка, поэтому мы создадим другой файл с заголовком. Для этого выполните следующую команду:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Затем объедините два файла, используя следующую команду:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Набор данных содержит несколько видов статистики для каждого сообщения электронной почты:

* Столбцы наподобие ***word\_freq\_WORD*** указывают процент упоминания слова *WORD* в сообщении. Например, если выражение *word\_freq\_make* равно 1, значит, 1 % всех слов в сообщении соответствует *make*.
* Столбцы наподобие ***char\_freq\_CHAR*** указывают процент всех символов в сообщении, которые соответствуют *CHAR*.
* ***capital\_run\_length\_longest*** — самая длинная последовательность прописных букв.
* ***capital\_run\_length\_average*** — средняя длина всех последовательностей прописных букв.
* ***capital\_run\_length\_total*** — общая длина всех последовательностей прописных букв.
* ***spam*** указывает, были ли сообщения признаны нежелательной почтой (1 — нежелательная почта, 0 —обычная почта).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Изучение набора данных с использованием Microsoft R Open
Давайте изучим данные и выполним некоторые основные задачи машинного обучения с использованием R. Компонент [Microsoft R Open](https://mran.revolutionanalytics.com/open/) предварительно установлен на виртуальной машине для обработки и анализа данных. Многопоточные математические библиотеки в этой версии R отличаются более высокой производительностью, чем однопоточные версии. Microsoft R Open предусматривает возможность воспроизведения за счет использования моментального снимка репозитория пакетов CRAN.

Чтобы получить копии примеров кода, используемых в этом пошаговом руководстве, клонируйте репозиторий **Azure-Machine-Learning-Data-Science** с помощью компонента Git, предварительно установленного на виртуальной машине. В программе командной строки Git выполните следующую команду:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Откройте окно терминала и запустите новый сеанс R с использованием интерактивной консоли R.

> [!NOTE]
> Для выполнения следующих процедур можно также использовать RStudio. Чтобы установить RStudio, в терминале выполните следующую команду: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Чтобы импортировать данные и настроить среду, выполните следующую команду:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Чтобы просмотреть сводные статистические данные по каждому столбцу, выполните следующую команду:

    summary(data)

Чтобы получить другое представление данных, выполните следующую команду:

    str(data)

В результате будет выведен тип каждой переменной и первые несколько значений в наборе данных.

Столбец *spam* распознан как целое число, но это на самом деле категориальная переменная (или факторная). Чтобы задать тип данных в этом столбце, выполните следующую команду:

    data$spam <- as.factor(data$spam)

Чтобы выполнить исследовательский анализ, используйте пакет [ggplot2](http://ggplot2.org/). Это популярная библиотека построения для R, уже установленная на виртуальной машине. Как видно в приведенных ранее сводных данных, у нас есть статистические данные по частоте использования восклицательного знака. Давайте построим графики частоты, используя следующие команды:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Так как нулевой показатель искажает график, давайте избавимся от него:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Выше показателя 1 наблюдается необычная плотность. Давайте взглянем только на эти данные.

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Затем разобьем их на наборы с обычной и нежелательной почтой.

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Эти примеры помогут вам построить аналогичные графики для других столбцов, чтобы проанализировать содержащиеся в них данные.

## <a name="train-and-test-an-ml-model"></a>Обучение и тестирование модели машинного обучения
Теперь давайте обучим несколько моделей машинного обучения для классификации сообщений электронной почты в наборе данных как содержащих обычную и нежелательную почту. В этом разделе мы обучим модель дерева принятия решений и модель случайного леса, а затем протестируем точность получаемых прогнозов.

> [!NOTE]
> Пакет rpart (рекурсивное секционирование и деревья регрессии), используемый в следующем коде, уже установлен на виртуальной машине для обработки и анализа данных.
>
>

Сначала разделим набор данных на обучающий и тестовый.

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

А затем создадим дерево принятия решений для классификации сообщений электронной почты.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Вот что мы получим:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Чтобы определить, насколько хорошо эта модель работает с обучающим набором данных, используйте следующий код:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Чтобы определить, насколько хорошо она работает с тестовым набором данных:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Теперь давайте испробуем модель случайного леса. Случайные леса обучают множество деревьев принятия решений и выводят класс, который представляет собой режим классификаций из всех отдельных деревьев принятия решений. Это более эффективный метод машинного обучения, так как он исправляет тенденцию к переобучению обучающего набора данных, которая возникает при использовании модели дерева принятия решений.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Развертывание модели в Студии машинного обучения Azure
[Студия машинного обучения Azure](https://studio.azureml.net/) — это облачная служба, упрощающая создание и развертывание моделей прогнозной аналитики. Одна из удобных функций этой службы — возможность публикации любой R-функции в виде веб-службы. Пакет R Машинного обучения Azure (AzureML) облегчает развертывание, позволяя выполнить его прямо из сеанса R на ВМ для обработки и анализа данных.

Чтобы развернуть код дерева принятия решений, приведенный в предыдущем разделе, необходимо войти в Студию машинного обучения Azure. Для этого необходимо указать идентификатор рабочей области и маркер авторизации. Чтобы найти эти значения и инициализировать с ними переменные Машинного обучения Azure, сделайте следующее:

В левом меню щелкните **Параметры** . Укажите значение в поле **Идентификатор рабочей области**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Выберите вкладку **Authorization Tokens** (Маркеры авторизации) в верхнем меню и укажите значение в поле **Primary Authorization Token** (Основной маркер авторизации).![3](./media/linux-dsvm-walkthrough/workspace-token.png)

Загрузите пакет **AzureML** и укажите маркер и идентификатор рабочей области в качестве значений переменных в сеансе R на ВМ для обработки и анализа данных.

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Давайте упростим модель, чтобы облегчить реализацию этой демонстрации. Выберите три переменные в самом приближенном к корневому дереве принятия решений и создайте новое дерево, используя только эти переменные:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Нам нужна функция прогнозирования, которая принимает в качестве входных данных признаки и возвращает прогнозируемые значения.

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Опубликуйте функцию predictSpam в Студии машинного обучения Azure с помощью функции **publishWebService** .

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Эта функция принимает функцию **predictSpam**, создает веб-службу **spamWebService** с определенными входными и выходными данными и возвращает сведения о новой конечной точке.

Просмотрите сведения об опубликованной веб-службе, включая ее конечную точку API и ключи доступа, используя следующую команду:

    spamWebService[[2]]

Чтобы испытать эту функцию на первых 10 строках тестового набора данных, выполните следующую команду:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Использование других доступных средств
В остальных разделах показано, как использовать некоторые средства, установленные на виртуальной машине Linux для обработки и анализа данных. Вот список рассматриваемых средств:

* XGBoost;
* Python;
* Jupyterhub;
* Rattle;
* PostgreSQL и Squirrel SQL;
* хранилище данных SQL Server.

## <a name="xgboost"></a>XGBoost;
[XGBoost](https://xgboost.readthedocs.org/en/latest/) — инструмент, предоставляющий быструю и точную реализацию увеличивающегося дерева.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

Его можно вызвать из командной строки или Python.

## <a name="python"></a>Python
Для разработки на языке Python на виртуальной машине для обработки и анализа данных установлены дистрибутивы Anaconda Python версий 2.7 и 3.5.

> [!NOTE]
> Дистрибутив Anaconda содержит компонент [Conda](http://conda.pydata.org/docs/index.html), который можно использовать для создания пользовательских сред для Python с различными установленными версиями и (или) пакетами.
>
>

Давайте считаем часть набора данных spambase и классифицируем сообщения с использованием метода опорных векторов в scikit-learn:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Для создания прогнозов используем следующую команду:

    clf.predict(X.ix[0:20, :])

Чтобы показать, как опубликовать конечную точку AzureML, упростим модель, используя три переменные, как при публикации модели R ранее.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Чтобы опубликовать модель в Студии машинного обучения Azure, используйте следующий код:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Это доступно только для Python 2.7 и еще не поддерживается для версии 3.5. Выполните код с использованием **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub;
Дистрибутив Anaconda на ВМ для обработки и анализа данных поставляется с записной книжкой Jupyter, кроссплатформенной средой для совместного использования кода Python, R или Julia и анализа. Доступ к записной книжке Jupyter можно получить через JupyterHub. Для входа введите учетные данные локального пользователя Linux по адресу ***https://\<DNS-имя или IP-адрес ВМ\>:8000/***. Все файлы конфигурации JupyterHub находятся в каталоге **/etc/jupyterhub**.

> [!NOTE]
> Чтобы использовать диспетчер пакетов Python (с помощью команды `pip`) из записной книжке Jupyter в текущем ядре, в ячейке кода можно использовать, например, такую команду:
```python
   import sys
   ! {sys.executable} -m pip install numpy -y
```
>
>

> [!NOTE]
> Чтобы использовать установщик Conda (с помощью команды `conda`) из записной книжке Jupyter в текущем ядре, в ячейке кода можно использовать, например, такую команду:
```python
   import sys
   ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
```
>
>

На виртуальной машине уже установлены некоторые примеры записных книжек:

* См. [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) для примера записной книжки Python.
* См. [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) для примера записной книжки **R**.
* См. [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) для еще одного примера записной книжки **Python**.

> [!NOTE]
> Язык Julia также можно использовать из командной строки на виртуальной машине Linux для обработки и анализа данных.
>
>

## <a name="rattle"></a>Rattle;
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (аналитическое средство R для легкого обучения) — это графическое средство R для интеллектуального анализа данных. Оно содержит интуитивно понятный интерфейс, упрощающий загрузку, изучение и преобразование данных, а также создание и анализ моделей.  Все возможности этого средства описаны в пошаговом руководстве [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) (Rattle: графический пользовательский интерфейс для интеллектуального анализа данных для R).

Установите и запустите Rattle с помощью следующих команд:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Rattle не требуется устанавливать на виртуальной машине для обработки и анализа данных. Но при загрузке Rattle может возникнуть запрос на установку дополнительных пакетов.
>
>

В Rattle используется интерфейс на основе вкладок. Большинство вкладок соответствует шагам [процесса обработки и анализа данных](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), например загрузка данных и их изучение. Процесс обработки и анализа данных выполняется слева направо, перемещаясь по вкладкам. Последняя вкладка содержит журнал команд R, выполняемых Rattle.

Чтобы загрузить и настроить набор данных:

* Чтобы загрузить файл, выберите вкладку **Data** (Данные).
* Щелкните селектор рядом с полем **Filename** (Имя файла) и выберите **spambaseHeaders.data**.
* Чтобы загрузить файл, нажмите кнопку **Execute** (Выполнить) в верхнем ряду кнопок. Должна появиться сводка по каждому столбцу, включая распознанный тип данных (входные данные, целевой объект или другой тип переменной) и количество уникальных значений.
* Rattle правильно распознал тип данных столбца **spam** как целевой объект. Выберите столбец нежелательной почты, а затем задайте для параметра **Target Data Type** (Целевой тип данных) значение **Categoric** (Категориальный).

Чтобы изучить данные:

* Выберите вкладку **Explore** (Изучение).
* Установите флажок **Summary** (Сводка), а затем нажмите кнопку **Execute** (Выполнить), чтобы просмотреть сведения о типах переменных и получить сводные статистические данные.
* Чтобы просмотреть другие статистические данные о каждой переменной, установите другие флажки, например **Describe** (Описание) или **Basics** (Основные сведения).

Вкладка **Explore** (Изучение) также позволяет создать множество полезных графиков. Чтобы построить гистограмму данных:

* Установите флажок **Distributions**(Дистрибутивы).
* Выберите **Histogram** (Гистограмма) для выражений **word_freq_remove** и **word_freq_you**.
* Нажмите кнопку **Execute**(Выполнить). В окне диаграммы должны отобразиться оба графика частоты, глядя на которые видно, что слово you встречается в сообщениях гораздо чаще, чем слово remove.

Графики корреляции не менее интересные. Чтобы создать график корреляции:

* Для поля **Type** (Тип) выберите **Correlation** (Корреляция).
* Нажмите кнопку **Execute**(Выполнить).
* Rattle рекомендует использовать не более 40 переменных. Нажмите кнопку **Yes** (Да), чтобы просмотреть график.

В результате можно получить интересные корреляции: например, technology (технологии) тесно коррелируют с "HP" и labs (задания). Данные также тесно связаны с "650", так как это код региона пользователей, предоставивших набор данных.

Числовые значения для корреляции между словами доступны в окне Explore (Изучение). Интересно, что technology (технология) отрицательно коррелирует с your (ваш) и money (деньги).

В Rattle также можно преобразовать набор данных для решения некоторых стандартных задач. Например, можно изменить масштаб признаков, добавить отсутствующие значения, обработать выбросы и удалить переменные или наблюдения с отсутствующими данными. Rattle также может определить правила взаимосвязей между наблюдениями и (или) переменными. Эти вкладки не рассматриваются в этом вводном пошаговом руководстве.

В Rattle также можно выполнять анализ кластеров. Давайте исключим некоторые признаки, чтобы облегчить чтение выходных данных. На вкладке **Data** (Данные) выберите **Ignore** (Пропустить) рядом с каждой переменной, за исключением следующих десяти элементов:

* word_freq_hp;
* word_freq_technology;
* word_freq_george;
* word_freq_remove;
* word_freq_your;
* word_freq_dollar;
* word_freq_money;
* capital_run_length_longest;
* word_freq_business;
* spam

Затем вернитесь на вкладку **Cluster** (Кластер), выберите **KMeans** (Метод K-средних) и установите для поля *Number of clusters* (Число кластеров) значение 4. Затем нажмите кнопку **Execute** (Выполнить). Результаты появятся в окне вывода. В одном кластере часто встречается слово "george" и "hp". Скорее всего, это деловое сообщение электронной почты.

Чтобы создать простую модель дерева принятия решений:

* Перейдите на вкладку **Model** (Модель).
* Для поля **Type** (Тип) выберите значение **Tree** (Дерево).
* Нажмите кнопку **Execute** (Выполнить), чтобы отобразить дерево в виде текста в окне вывода.
* Нажмите кнопку **Draw** (Рисовать), чтобы просмотреть графическое представление. Оно похоже на дерево, полученное ранее при использовании *rpart*.

Одна из удобных функций Rattle — возможность выполнять несколько методов машинного обучения и быстро их оценивать. Вот что нужно сделать:

* Для поля **Type** (Тип) выберите значение **All** (Все).
* Нажмите кнопку **Execute**(Выполнить).
* По завершении выполнения можно щелкнуть любой отдельный **тип**, например **SVM**, и просмотреть результаты.
* Можно также сравнить производительность моделей с использованием набора для проверки на вкладке **Evaluate** (Оценка). Например, если выбрать **матрицу ошибок** , отобразится матрица неточностей, общий показатель ошибок и ошибка усредненного класса для каждой модели в наборе для проверки.
* Вы также можете построить кривые ROC, выполнить анализ чувствительности и выполнить другие оценки модели.

Завершив создание моделей, выберите вкладку **Log** (Журнал), чтобы просмотреть код R, выполняемый Rattle во время сеанса. Чтобы сохранить его, нажмите кнопку **Export** (Экспорт).

> [!NOTE]
> В текущем выпуске Rattle есть ошибка. Чтобы изменить сценарий или использовать его для воспроизведения шагов позже, необходимо вставить символ # перед фразой *Export this log...* в тексте журнала.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL и Squirrel SQL;
На ВМ для обработки и анализа данных установлен компонент PostgreSQL. PostgreSQL — продвинутая реляционная база данных с открытым исходным кодом. В этом разделе показано, как загрузить наш набор данных нежелательной почты в PostgreSQL, а затем выполнить к нему запрос.

Перед загрузкой данных необходимо разрешить выполнять проверку подлинности с использованием пароля на узле localhost. В окне командной строки:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

В нижней части файла конфигурации расположены несколько строк, описывающие разрешенные подключения.

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

В строке IPv4 local connections замените ident на md5, чтобы можно было выполнить вход с помощью имени пользователя и пароля.

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

И перезапустите службу Postgres:

    sudo systemctl restart postgresql

Чтобы запустить psql, интерактивный терминал для PostgreSQL, от имени встроенного пользователя Postgres, выполните из командной строки следующую команду:

    sudo -u postgres psql

Создайте учетную запись пользователя, используя имя пользователя учетной записи Linux, в которую выполнен вход, и задайте для нее пароль:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Затем войдите в psql от имени пользователя:

    psql

Импортируйте данные в новую базу данных:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Теперь давайте изучим данные и выполним некоторые запросы с помощью **Squirrel SQL**, графического средства, позволяющего взаимодействовать с базами данных через драйвер JDBC.

Чтобы начать работу, запустите Squirrel SQL из меню приложений. Чтобы настроить драйвер, сделайте следующее:

* Выберите **Windows**, а затем — **View Drivers** (Просмотреть драйверы).
* Щелкните **PostgreSQL** правой кнопкой мыши и выберите **Modify Driver** (Изменить драйвер).
* Выберите **Extra Class Path** (Путь к дополнительным классам), а затем щелкните **Добавить**.
* Введите ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** в поле **Имя файла**.
* Щелкните **Open**(Открыть).
* Щелкните "Список драйверов", выберите **org.postgresql.Driver** в области **Имя класса** и нажмите кнопку **ОК**.

Чтобы установить подключение к локальному серверу:

* Выберите **Windows**, а затем — **View Aliases** (Просмотреть псевдонимы).
* Нажмите кнопку **+** , чтобы создать новый псевдоним.
* Присвойте ему имя *База данных нежелательной почты* и выберите **PostgreSQL** в раскрывающемся списке **Драйвер**.
* В качестве URL-адреса укажите *jdbc:postgresql://localhost/spam*.
* Введите *имя пользователя* и *пароль*.
* Последовательно выберите **ОК**.
* Чтобы открыть окно **Подключение**, дважды щелкните псевдоним ***База данных нежелательной почты***.
* Нажмите кнопку **Подключиться**.

Чтобы выполнить запросы:

* Выберите вкладку **SQL** .
* Введите простой запрос, например `SELECT * from data;` , в текстовом поле запроса в верхней части вкладки SQL.
* Чтобы запустить запрос, нажмите клавиши **Ctrl+Enter** . По умолчанию Squirrel SQL возвращает первые 100 строк из запроса.

Для изучения этих данных можно выполнить множество других запросов. Например, как отличается частота упоминания слова *make* в нежелательной и обычной почте?

    SELECT avg(word_freq_make), spam from data group by spam;

Или каковы характеристики сообщений электронной почты, в которых часто встречается *3d*?

    SELECT * from data order by word_freq_3d desc;

Большинство сообщений электронной почты, в которых часто встречается *3d*, — очевидно нежелательная почта. Поэтому может быть полезно создать прогнозную модель для классификации сообщений.

Если вы хотите выполнять задачи машинного обучения с данными, хранящимися в базе данных PostgreSQL, рассмотрите возможность использования [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>хранилище данных SQL Server.
Хранилище данных SQL Azure — это развернутая в облаке база данных, способная обрабатывать большие объемы реляционных и нереляционных данных. Дополнительные сведения см. в статье [Что такое хранилище данных SQL Azure?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Чтобы подключиться к хранилищу данных и создать таблицу, выполните следующую команду из командной строки:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Затем в командной строке sqlcmd выполните следующую команду:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Скопируйте данные с помощью bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Символы конца строки в скачанном файле представлены в стиле Windows, но bcp ожидает стиль UNIX, поэтому нужно сообщить это bcp с помощью параметра -r.
>
>

Выполните запрос с использованием sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Запросы также можно выполнять с помощью Squirrel SQL. Выполните аналогичные действия для PostgreSQL с использованием драйвера JDBC Microsoft MSSQL Server, который можно найти в каталоге ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Дополнительная информация
Обзор разделов с пошаговыми руководствами по задачам, которые входят в процесс обработки и анализа данных в Azure, см. в статье [Процесс обработки и анализа данных группы](http://aka.ms/datascienceprocess).

В статье [Пошаговые руководства по процессу обработки и анализа данных](../team-data-science-process/walkthroughs.md)представлены другие пошаговые руководства, которые демонстрируют этапы процесса обработки и анализа данных группы для конкретных сценариев. В пошаговых руководствах также показано, как объединить облачные и локальные инструменты и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение.

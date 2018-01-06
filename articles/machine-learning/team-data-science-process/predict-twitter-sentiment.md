---
title: "Прогноз мнений Twitter с внедряемые объекты word с помощью командного процесса обработки и анализа данных в Azure | Документы Microsoft"
description: "Действия, необходимые для выполнения проектов обработки и анализа данных."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: 20bc3f31897cec4a3cec9ca409062229133102f5
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Прогноз мнений Twitter с внедряемые объекты word с помощью командного процесса обработки и анализа данных

В этой статье показано, как для эффективной совместной работы с помощью _Word2Vec_ word, применяя алгоритм и _внедрение мнений конкретного слова (SSWE)_ алгоритм для прогнозирования мнений Twitter с [Машинного обучения azure](../preview/index.yml). Дополнительные сведения о прогнозирование полярности мнений Twitter. в разделе [MachineLearningSamples TwitterSentimentPrediction репозитория](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) на GitHub. Ключом к проведению эффективных совместная работа над проектами обработки и анализа данных является стандартизировать структуры и документации проектов с жизненным циклом установленного-обработки и анализа данных. [Процесса обработки и анализа данных Team (TDSP)](overview.md) предоставляет этот тип структурированных [жизненного цикла](lifecycle.md). 

Создание проектов обработки и анализа данных с _TDSP шаблона_ предоставляет стандартизированные framework для проектов машинного обучения Azure. Ранее, выпуска командой TDSP [репозитории GitHub для TDSP структуры проекта и шаблонов](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Теперь проектов машинного обучения, реализованным с [TDSP шаблонов машинного обучения Azure](https://github.com/amlsamples/tdsp) включены. Инструкции см. в разделе способы использования [TDSP структура проектов с помощью шаблона TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) в машинном обучении Azure. 


## <a name="twitter-sentiment-polarity-sample"></a>Полярность мнений Twitter

В этой статье используется пример объясняется, как создать и выполнять проект машинного обучения. В образце используется структура TDSP и шаблонов в Azure Machine Learning Workbench. Полный пример приведен в [в данном пошаговом руководстве](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Задачи моделирования прогнозирует полярности мнений (положительное или отрицательное), используя текст из твиты. В этой статье описываются задачи моделирования данных, описанные в этом пошаговом руководстве. Пошаговом руководстве рассматриваются следующие задачи:

- Просмотр данных, обучения и развертывания модели машинного обучения, решить проблему прогноза, описанное в обзоре вариантов использования. [Twitter настроении](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) используется для выполнения следующих задач.
- Выполнение проекта с помощью шаблона TDSP из машинного обучения Azure для этого проекта. Жизненный цикл TDSP используется для выполнения проекта и создания отчетов.
- Ввода в эксплуатацию решений непосредственно из машинного обучения Azure в службе контейнера Azure.

Проект выделены следующие функции машинного обучения Azure.

- Создание экземпляра и использование структуры TDSP.
- Выполнение кода в Azure Machine Learning Workbench.
- Простоту практического применения в контейнер службы с помощью Docker и Kubernetes.

## <a name="team-data-science-process"></a>Процесс обработки и анализа данных группы
Для выполнения этого примера, используйте шаблоны проектов TDSP структуры и документации в Azure Machine Learning Workbench. В этом образце реализуется [жизненного цикла TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), как показано на следующем рисунке:

![Жизненный цикл процесса обработки и анализа данных группы](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

В Azure Machine Learning рабочей среды на основе создается проект TDSP [эти инструкции](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), как показано на следующем рисунке:

![Создание TDSP в Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Получение данных и подготовка](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Первым шагом в этом образце является скачать набор данных sentiment140 и разделить данные на обучающие и проверочные наборы данных. Sentiment140 набор данных содержит фактическое содержимое твит (с настроения удалены). Набор данных также содержит полярности каждого твит (отрицательное = 0, положительное = 4) с нейтральным твиты удалены. Деления данных обучающих данных 1,3 миллиона строк, а проверочных данных имеет 320,000 строк.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Модель разработки](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

При разработке модели данных является следующим шагом в образце. Задачи моделирования состоит из трех частей:

- Функция engineering: создавать признаки для модели с помощью различных слов, внедрение алгоритмов. 
- Создание модели: обучения различных моделей для прогнозирования мнений вводимого текста. Примеры этих моделей _логистической регрессии_ и _градиентный Бустинг_.
- Модель оценки: оценки обученных моделей по проверочных данных.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Конструируются](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Алгоритмы Word2Vec и SSWE, используются для создания внедренных word. 


#### <a name="word2vec-algorithm"></a>Алгоритм Word2Vec

Skip-Gram модели используется алгоритм Word2Vec. Эта модель объясняется в документе по Tomas Mikolov и т. п. «[Распределенного представления ключевых слов и фраз и их композициональность. Усовершенствованные возможности нейронной сведения о системах обработки. ](https://arxiv.org/abs/1310.4546)" 2013.

Skip-Gram модель является неполной нейронной сети. Введенное значение целевой слово, которое кодируется как вектор горячей один, который используется для прогнозирования ближайших слова. Если **V** — размер термины, то размер выходного слоя __C * V__ C является размер окна контекста. На следующем рисунке показана архитектуру, которая основана на модели пропустить-грамм:

![Skip-Gram модели](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Подробные механизмы Word2Vec алгоритм и пропустить-Gram модели выходят за рамки данного образца. Дополнительные сведения см. следующие ссылки:

- [Код 02_A_Word2Vec.py с примерами упоминаемого TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Векторные представления слов).
- [Как именно работает word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Замечания о Contrastive оценка шума и отрицательное выборки](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Внедрение Word мнений конкретного алгоритма
Алгоритм SSWE пытается преодолеть недоработки Word2Vec алгоритма, где слов с контекстами аналогичные и противоположными полярности может иметь аналогичные векторов word. Сходство может привести к алгоритм Word2Vec точно выполнение задач, таких как анализ мнений. Алгоритм SSWE пытается обработать этот недостаток особенно, включая полярности предложения и контекст word в его функции потерь.

В образце используется вариант алгоритма SSWE следующим образом:

- Как для исходного _ngram_ или поврежденные _ngram_ используются в качестве входных данных.
- Объект Ранжирующая функция потери стиль около петель монитора используется для потери синтаксические и семантические потери.
- Функция ultimate потери — взвешенного сочетание потери синтаксические и семантические потери.
- Для простоты только семантического перекрестной энтропии используется в качестве функции потерь.

В этом примере, даже с простой функцию потерь производительности внедрения SSWE лучше, чем внедрение Word2Vec. На следующем рисунке показана convolutional модель, которая используется для создания внедрение мнений словам:

![Модель нейронной сети по SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

После завершения процесса обучения двух внедрение файлов в формате значений, разделенных табуляцией (TSV) создаются для моделирования рабочей области.

Дополнительные сведения об алгоритмах SSWE см. в статье Duyu Tang и т. п. «[Обучения конкретного мнений Word внедрение классификация мнений Twitter](http://www.aclweb.org/anthology/P14-1146).» Ассоциации для вычислительных Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Создание моделей](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
После слова векторов создаются с помощью алгоритма SSWE или Word2Vec, модели классификации, подготовленные для прогнозирования полярности фактическое мнений. Два типа функций: Word2Vec и SSWE, применяются к две модели: градиентный Бустинг модели и модели логистической регрессии. В результате для четырех разных моделей обучения.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Модель оценки](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
После обучения модели модели используются для тестирования Twitter текстовых данных и оценки производительности каждой модели. Образец оценивает следующие четыре модели:

- Градиент Boosting через SSWE внедрения.
- Алгоритм логистической регрессии через SSWE внедрения.
- Градиент Boosting через Word2Vec внедрения.
- Алгоритм логистической регрессии через Word2Vec внедрения.

На следующем рисунке показано сравнение производительности четыре модели:

![Получатель операционные характеристики сравнения моделей (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Градиентный Бустинг модели с помощью функции SSWE покажет лучшую производительность при сравнении моделей с помощью площадь под кривой (AUC) метрику.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Развертывание](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Последним шагом является развертывание модели прогнозирования обученной мнений в веб-службу в кластере в контейнере службы Azure. В образце используется модель градиентный Бустинг внедрения алгоритму SSWE как обученной модели. Среда ввода в эксплуатацию подготавливает Docker и Kubernetes в кластере для развертывания веб службы, как показано на следующем рисунке: 

![Панель мониторинга Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Дополнительные сведения о процессе ввода в эксплуатацию см. в разделе [развертывание модели машинного обучения Azure через веб-службу](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Заключение

В этой статье вы узнали, как для обучения модели внедрения word с помощью Word2Vec и внедрение Word мнений конкретных алгоритмов. Извлеченные внедряемые объекты были использованы в качестве функции для обучения нескольких моделей для прогнозирования оценок мнений Twitter текстовых данных. Функция SSWE, используется с моделью градиентный Бустинг предлагает наивысшую производительность. Модель затем развернуть в реальном времени веб-службы в службе контейнера с помощью Azure Machine Learning Workbench.


## <a name="references"></a>Ссылки

* [Процесс обработки и анализа данных для команды](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Использование командного процесса обработки и анализа данных (TDSP) в службе "Машинное обучение Microsoft Azure"](https://aka.ms/how-to-use-tdsp-in-aml).
* [Шаблоны проектов TDSP для машинного обучения Azure](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/preview/).
* [Доход США набора данных из репозитория UCI машинного Обучения](https://archive.ics.uci.edu/ml/datasets/adult)
* [Распознавание биомедицинских сущности с помощью шаблонов TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov Tomas, и т. п. «Распределенные представления ключевых слов и фраз и их композициональность. Перемещает нейронной сведения о системах обработки.» 2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu, и т. п. «Обучения словам мнений внедрение классификация мнений Twitter». СПИСОК УПРАВЛЕНИЯ ДОСТУПОМ (1). 2014.](http://www.aclweb.org/anthology/P14-1146)

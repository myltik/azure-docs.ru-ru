---
title: "Предсказать мнений Twitter, внедряемые объекты word, с помощью процесса обработки и анализа данных командного - Azure | Документы Microsoft"
description: "Порядок выполнения проектов по обработке и анализу данных"
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
ms.openlocfilehash: fe1c87df40102a62e1e0c8873b25fa3df7d743bc
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="predict-twitter-sentiment-with-word-embeddings-using-the-team-data-science-process"></a>Предсказать мнений Twitter, внедряемые объекты word, с помощью командного процесса обработки и анализа данных

В этой статье показано, как для эффективной совместной работы при использовании **Word2Vec** word, применяя алгоритм и **мнений конкретного слова внедрение (SSWE) алгоритм** для прогнозирования мнений Twitter с [Машинного обучения azure](../preview/index.yml). Дополнительные сведения о задача прогнозирования twitter полярности мнений, см. в разделе [репозитория](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction). Ключом к проведению эффективных совместная работа над проектами обработки и анализа данных является стандартизировать структуры и документации проектов с жизненным циклом обработки и анализа данных. [Процесса обработки и анализа данных Team (TDSP)](overview.md) предоставляет такое структурированных [жизненного цикла](lifecycle.md). 

Создание проектов научных вычислений с **TDSP шаблона** предоставляет стандартизированные платформа для проектов машинного обучения Azure. Ранее выпустила TDSP team [репозитории GitHub для TDSP структуры проекта и шаблонов](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Теперь процесс создания проектов машинного обучения Azure, экземпляры которых создаются с [TDSP шаблоны структуры и документации для машинного обучения Azure](https://github.com/amlsamples/tdsp) был включен. Инструкции по использованию TDSP структуры и шаблоны в машинном обучении Azure см. в разделе [структуры проектов с помощью шаблона процесса обработки и анализа данных командного](../preview/how-to-use-tdsp-in-azure-ml.md). 


## <a name="the-sentiment-polarity-sample"></a>Полярность мнений

Образец, в котором показано, как создать и выполнить машинного обучения структуры процесса обработки и анализа данных командного проекта и шаблоны в установку обучения рабочих Azure компьютеров предоставляется в этом [Пошаговое руководство](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Задачи моделирования состоит в прогнозировании мнениях полярности (положительное или отрицательное) текста из твиты. В этой статье описываются моделирования задач, описанных в этом пошаговом руководстве. Пошаговом руководстве рассматриваются следующие задачи:

- Изучение данных, обучение и развертывание модели машинного обучения для выполнения задачи прогнозирования. Задача описана в обзоре вариантов использования. Для этой цели [мнений Twitter данных](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) используется.
- Выполнение проекта в службе "Машинное обучение Microsoft Azure" на основе предоставляемого службой шаблона для командного процесса обработки и анализа данных (TDSP). Жизненный цикл TDSP используется для выполнения проекта и создания отчетов.
- Ввод решения в эксплуатацию напрямую из службы "Машинное обучение Microsoft Azure" в Службе контейнеров Azure.

Проект выделяет несколько функций машинного обучения Azure, такие при создании экземпляра структуры TDSP и использование, выполнение кода в Azure Machine обучения рабочих сред и простоту практического применения в контейнере службы Azure, с помощью Docker и Kubernetes.

## <a name="team-data-science-process"></a>Процесс обработки и анализа данных группы
Для выполнения этого примера используйте шаблоны проектов TDSP структуры и документации. Следует [TDSP жизненного цикла]((https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)). Проект создается в соответствии с инструкциями [здесь](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).

![Жизненный цикл процесса обработки и анализа данных группы](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

![Создать экземпляр TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Получение и анализ данных](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding).
Первым шагом в этом образце является скачать набор данных sentiment140 и разделить ее на обучающий и проверочный наборы данных. Sentiment140 набор данных содержит фактическое содержимое твит (с удалены эмотиконы), а также полярности каждого твит (отрицательное = 0, положительное = 4), с нейтральным твиты удалены. При разделении, полученный обучающих данных 1,3 миллиона строк, а проверочных данных имеет 320 тысяч строк.


## <a name="modelinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Моделирование](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Эта часть образца подразделяются на три части:
 
- **Функция Engineering** соответствует создания компонентов с использованием различных слов, внедрение алгоритмов. 
- **Создание модели** сделок учебному различных моделей, например _логистической регрессии_ и _градиентный бустинг_ для прогнозирования мнений вводимого текста. 
- **Модель оценки** применяет обученной модели проверочных данных.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Конструируются](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec и SSWE — это слово, внедрение алгоритмы, используемые здесь, для создания внедренных word. 


#### <a name="word2vec"></a>Word2Vec

В режиме Skipgram используется алгоритм Word2Vec. Этот способ формирования внедряемые объекты word объясняется в документе Tomas Mikolov et al.: [распределенного представления слов и фраз и их композициональность. Усовершенствованные возможности нейронной сведения о системах обработки. 2013.](https://arxiv.org/abs/1310.4546).

Skip-gram является неполной нейронной сети. Его входные данные — слово целевой кодировке один горячий вектор, который используется для прогнозирования ближайших слова. Если **V** — размер термины, то размер выходного слоя будет __C * V__ C является размер окна контекста. Skip-gram основе архитектура показана на следующем рисунке:

![Skip-gram модели](./media/predict-twitter-sentiment/skip-gram-model.PNG)

***Skip-gram модели***

Подробные механизм, позволяющий модели алгоритм и пропустить грамм word2vec выходят за рамки данного образца. Читатели заинтересованы в более подробные сведения о его работы можно найти следующие ссылки:

- [Код 02_A_Word2Vec.py ссылки на примеры TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)
- [Представление вектор слов](https://www.tensorflow.org/tutorials/word2vec)
- [Как именно работает word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Замечания о Contrastive оценка шума и отрицательное выборки](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)


#### <a name="sswe"></a>SSWE
**Мнений конкретного слова внедрение (SSWE) алгоритм** пытается преодолеть недоработки Word2vec алгоритма, слов с контекстами аналогичные и противоположными полярности может иметь аналогичные векторов word. Это сходство означает, что Word2vec не может выполнить точно для выполнения задач, таких как анализ мнений. Алгоритм SSWE пытается обработать этот недостаток особенно, включая полярности предложения и контекст word в его функции потерь.

В этом образце используется вариант SSWE. SSWE использует исходное ngram и поврежденный ngram как входные данные, стиль ранжирования бизнеса функцию потерь для потери синтаксические и семантические потери. Функции потерь Ultimate — взвешенного сочетание потери синтаксические и семантические потери. С целью упрощения только семантического перекрестной энтропии используется в качестве функции потерь. Как видно в более поздней версии, даже с этого простым функции потерь производительности внедрения SSWE лучше, чем внедрение Word2Vec.

Модели SSWE впечатляющие нейронной сети, используемой в этом образце показан на следующем рисунке:

![Сравнение моделей ROC](./media/predict-twitter-sentiment/embedding-model-2.PNG)

***Convolutional модель для создания внедрение словам мнений.***


После завершения процесса обучения двух внедрение файлов в формате TSV создаются для моделирования рабочей области.

Дополнительные сведения об алгоритмах SSWE см. в документе Duyu Tang et al.: [обучения мнений конкретного слова внедрение классификация мнений Twitter. СПИСОК УПРАВЛЕНИЯ ДОСТУПОМ (1). 2014.](http://www.aclweb.org/anthology/P14-1146) 


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Создание моделей](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Векторы word были созданы с помощью алгоритмов SSWE или Word2vec, следующим шагом после для обучения модели классификации для прогнозирования полярности фактическое мнений. Два типа функций Word2Vec и SSWE, применяются к две модели GBM модели и модели логистической регрессии. Поэтому четыре различных моделей прошла обучение.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Модель оценки](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Теперь используется четыре модели, обучение в предыдущем шаге в проверочных данных для оценки производительности модели. 

1. Градиент Boosting через SSWE внедрения
2. Алгоритм логистической регрессии через SSWE внедрения
3. Градиент Boosting через Word2Vec внедрения
4. Алгоритм логистической регрессии через Word2Vec внедрения

![Сравнение моделей ROC](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

GBM модели с функциями SSWE наилучшим является использование AUC метрику.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Развертывание](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Эта часть развертывает модель прогнозирования обученной мнений (внедрение SSWE + GBM модели) для веб-службы в кластере в контейнере службы Azure (ACS). Среда ввода в эксплуатацию подготавливает в кластере Docker и Kubernetes для управления развертыванием веб службы. Дополнительные сведения о вводе в эксплуатацию вы найдете [здесь](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

![kubenetes_dashboard](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)


## <a name="conclusion"></a>Заключение

Были представлены сведения о том, как обучить модель внедрения word с помощью алгоритмов Word2Vec и SSWE и извлеченные внедряемые объекты были использованы в качестве функции для обучения нескольких моделей для прогнозирования оценок мнений Twitter текстовых данных. Компонент конкретных формулировка Embeddings(SSWE) мнений с градиентной повышенного дерева модели предлагает наилучшую производительность. Затем эта модель была развернута в режиме реального времени веб-службы в службах Azure контейнера с помощью Azure машины обучения рабочих сред.


## <a name="references"></a>Ссылки

* [Процесс обработки и анализа данных для команды](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Использование командного процесса обработки и анализа данных (TDSP) в службе "Машинное обучение Microsoft Azure"](https://aka.ms/how-to-use-tdsp-in-aml).
* [Шаблон проекта TDSP для службы "Машинное обучение Microsoft Azure"](https://aka.ms/tdspamlgithubrepo).
* [Azure ML рабочих сред](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Набор данных о доходах граждан США из репозитория ML UCI](https://archive.ics.uci.edu/ml/datasets/adult).
* [С помощью шаблона TDSP биомедицинских распознавание сущности](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov Tomas, и т. п. Distributed representations of words and phrases and their compositionality (Распределенные представления слов и фраз и их композиционность). Усовершенствованные возможности нейронной сведения о системах обработки. 2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu, и т. п. «Обучения словам мнений внедрение классификация мнений Twitter». СПИСОК УПРАВЛЕНИЯ ДОСТУПОМ (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
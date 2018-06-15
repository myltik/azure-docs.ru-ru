---
title: Предсказание тональности высказываний в Twitter на основе векторного представления слов с помощью процесса обработки и анализа данных группы в Azure | Документация Майкрософт
description: Порядок выполнения проектов по обработке и анализу данных
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: deguhath
ms.openlocfilehash: f47668cd706b78977418925d64eca583d7878cd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838315"
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Предсказание тональности высказываний в Twitter на основе векторного представления слов с помощью процесса обработки и анализа данных группы

В этой статье описано, как повысить эффективность совместной работы, используя алгоритм векторного представления слов _Word2Vec_ и алгоритм _векторного представления слов, характерных при составлении картины тональности (SSWE)_ для предсказания тональности высказываний в Twitter на основе [технологии машинного обучения Azure](../service/index.yml). Дополнительные сведения о предсказании тональности высказываний в Twitter см. в [репозитории MachineLearningSamples-TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) в GitHub. Стандартизация структуры и документации проектов по обработке и анализу данных, которые привязаны к стабильному жизненному циклу обработки и анализа данных, — это главное условие успешной совместной работы в команде специалистов по обработке и анализу данных. [Процесс обработки и анализа данных группы (TDSP)](overview.md) предоставляет этот тип структурированного [жизненного цикла](lifecycle.md). 

Создавая проекты по обработке и анализу данных на основе шаблона _TDSP_, вы формируете стандартизированную платформу для проектов службы "Машинное обучение Azure". Ранее команда TDSP выпустила [репозиторий GitHub с примерами структуры и шаблонов проектов TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Теперь в нем доступны проекты службы "Машинное обучение", созданные на основе специальных [шаблонов TDSP](https://github.com/amlsamples/tdsp). Инструкции по их использованию в службе "Машинное обучение Azure" см. в статье [Структурирование проектов с помощью шаблона процесса обработки и анализа данных группы](../desktop-workbench/how-to-use-tdsp-in-azure-ml.md). 


## <a name="twitter-sentiment-polarity-sample"></a>Пример полярности тональности высказываний в Twitter

В этой статье приведен пример, с помощью которого можно создать и выполнить проект службы "Машинное обучение". В этом примере применяется структура и шаблоны TDSP в Azure Machine Learning Workbench. Полный пример см. в [этом пошаговом руководстве](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Задача моделирования предсказывает полярность тональности (положительная или отрицательная) на основе текста твитов. В этой статье приведены основные задачи моделирования данных, описанные в этом пошаговом руководстве. В рамках этого руководства рассматриваются следующие задачи:

- Изучение данных, обучение и развертывание модели машинного обучения для выполнения задачи прогнозирования. Задача описана в обзоре вариантов использования. При выполнении этих задач используются [данные тональности высказываний в Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip).
- Выполнение проекта с помощью шаблона TDSP из службы "Машинное обучение Azure". При выполнении проекта и создании отчетов используется жизненный цикл TDSP.
- Ввод решения в эксплуатацию напрямую из службы "Машинное обучение Azure" в Службе контейнеров Azure.

Проект поддерживает следующие функции службы "Машинное обучение Azure":

- создание и использование структуры TDSP;
- выполнение кода в Azure Machine Learning Workbench;
- простая реализация в службе контейнеров с помощью Docker и Kubernetes.

## <a name="team-data-science-process"></a>Процесс обработки и анализа данных группы
Чтобы выполнить этот пример, требуются шаблоны структуры проекта и документации TDSP в Azure Machine Learning Workbench. Этот пример реализует [жизненный цикл TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), как показано на следующем рисунке:

![Жизненный цикл процесса обработки и анализа данных группы](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

Проект TDSP создан в Azure Machine Learning Workbench на основе [этих инструкций](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), как показано на следующем рисунке:

![Создание TDSP в Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Получение и подготовка данных](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Сначала необходимо загрузить набор данных sentiment140 и разделить его на два меньших: для обучения и тестирования. Набор данных sentiment140 содержит фактическое содержимое твита (без смайликов), а также полярность высказывания каждого твита (положительные твиты имеют значение 0, а отрицательные — 4). Нейтральные твиты удалены. После разделения данные для обучения содержат 1,3 млн строк, а данные для тестирования — 320 000 строк.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Разработка модели](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Следующий шаг в примере заключается в разработке модели данных. Задача моделирования состоит из трех частей:

- Проектирование признаков — создание признаков модели с помощью алгоритмов векторного представления слов. 
- Создание модели — обучение различных моделей прогнозировать тональность входных данных. К таким моделям относятся _Логистическая регрессия_ и _Градиентный бустинг_.
- Оценка модели — оценка обученных моделей на основе данных для тестирования.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Проектирование признаков](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Для векторного представления слов используются алгоритмы Word2Vec и SSWE. 


#### <a name="word2vec-algorithm"></a>Алгоритм Word2Vec

Алгоритм Word2Vec используется в модели Skip-Gram. Сведения об этой модели см. в работе Томаса Миколова (Tomas Mikolov) и его коллег [Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems](https://arxiv.org/abs/1310.4546) (Распределенные представления слов и фраз и их композиционность. Улучшения в нейросетевых системах обработки информации) за 2013 год.

Модель Skip-Gram — это небольшая нейронная сеть. В качестве входных данных используется целое слово, закодированное как прямой унитарный вектор, на основе которого прогнозируются ближайшие слова. Если **V** — это размер словаря, то размер выходного слоя составляет __C*V__. В этом случае C — размер контекстного окна. На схеме ниже приведена архитектура, основанная на модели Skip-Gram:

![Модель Skip-Gram](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Подробные принципы действия алгоритма Word2Vec и модели Skip-Gram не рассматриваются в рамках этого примера. Дополнительные сведения см. в следующих ресурсах.

- [Код 02_A_Word2Vec.py с указанными примерами TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Векторные представления слов).
- [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Принцип работы Word2Vec)
- [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Примечания об оценке сопоставления шума и отрицательной выборке)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Алгоритм векторного представления слов, характерных при составлении картины тональности
Алгоритм SSWE позволяет решить недостаток алгоритма Word2Vec, где слова с похожими контекстами и противоположной полярностю могут иметь похожие векторы. Эти сходства влияют на точность выполнения задач алгоритмом Word2Vec, например анализ тональности. Алгоритм SSWE устраняет этот недостаток за счет объединения полярности предложения и контекста слова в функции потерь.

В примере алгоритм SSWE используется следующим образом:

- В качестве входных данных используется как исходная _N-грамма_, так и поврежденная _N-грамма_.
- Кусочно-линейная ранжирующая функция потерь используется как для определения синтаксической, так и семантической потерь.
- Итоговая функция потерь состоит из сочетания функций синтаксической и семантической потерь.
- Для простоты в качестве функции потерь используется только семантическая перекрестная энтропия.

Из этого примера видно, что даже с простой функцией потерь эффективность алгоритма векторного представления SSWE лучше, чем алгоритма Word2Vec. На схеме ниже приведена сверточная модель, на основе которой создается векторное представление слов, характерных при составлении картины тональности.

![Модель нейронной сети на основе алгоритма SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

После обучения на этапе моделирования создается два TSV-файла векторного представления.

Дополнительные сведения об алгоритмах SSWE см. в работе Дую Тенга (Duyu Tang) и его коллег [Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146) Association for Computational Linguistics (1) (Изучение векторного представления слов, характерных при составлении картины тональности высказываний в Twitter. Общество компьютерной лингвистики (1)) за 2014 год.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Создание модели](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
После создания векторов слов с помощью алгоритма SSWE или Word2Vec модели классификации обучают предсказывать фактическую полярность тональности. К двум моделям ("Градиентный бустинг" и "Логистическая регрессия") применяются два типа функций (Word2Vec и SSWE). В результате обучаются четыре разные модели.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Оценка модели](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
После обучения модели используются для проверки текстовых данных Twitter, после чего оценивается эффективность каждой модели. В примере оцениваются следующие четыре модели:

- градиентный бустинг на основе алгоритма векторного представления SSWE;
- логистическая регрессия на основе алгоритма векторного представления SSWE;
- градиентный бустинг на основе алгоритма векторного представления Word2Vec;
- логистическая регрессия на основе алгоритма векторного представления Word2Vec.

На графике ниже показаны результаты сравнения эффективности четырех моделей.

![Сравнение моделей в виде кривых рабочих характеристик приемника (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Если сравнивать модели по методу AUC "Площадь под кривой" (AUC), модель "Градиентный бустинг" на основе алгоритма SSWE обеспечивает наилучшую эффективность.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Развертывание](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Последний шаг заключается в развертывании модели прогнозирования тональности в веб-службу кластера в Службе контейнеров Azure. В примере в качестве обученной используется модель "Градиентный бустинг" на основе алгоритма векторного представления SSWE. Среда ввода в эксплуатацию подготавливает Docker и Kubernetes в кластере, чтобы управлять развертыванием веб-службы, как показано на рисунке ниже. 

![Панель мониторинга Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Дополнительные сведения о вводе в эксплуатацию см. в статье [Развертывание модели машинного обучения в качестве веб-службы](../desktop-workbench/model-management-service-deploy.md).

## <a name="conclusion"></a>Заключение

Работая с этой статьей, вы узнали, как обучить модель векторного представления слов с помощью алгоритма Word2Vec и алгоритма векторного представления слов, характерных при составлении картины тональности. При обучении нескольких моделей предсказывать тональность высказываний на основе текстовых данных Twitter в качестве признаков использовались извлеченные векторные представления. С помощью алгоритма SSWE удалось получить наилучшую эффективность модели "Градиентный бустинг". Затем мы развернули модель как веб-службу в режиме реального времени в службе контейнеров с помощью Azure Machine Learning Workbench.


## <a name="references"></a>Ссылки

* [Что такое процесс обработки и анализа данных группы?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Использование командного процесса обработки и анализа данных (TDSP) в службе "Машинное обучение Microsoft Azure"](https://aka.ms/how-to-use-tdsp-in-aml).
* [Шаблон проекта TDSP для службы "Машинное обучение Azure"](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../service/index.yml).
* [Набор данных о доходах граждан США из репозитория ML UCI](https://archive.ics.uci.edu/ml/datasets/adult)
* [Распознавание биомедицинских сущностей с помощью шаблона командного процесса обработки и анализа данных (TDSP)](../desktop-workbench/scenario-tdsp-biomedical-recognition.md)
* [Работа Томаса Миколова (Tomas Mikolov) и его коллег Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems (Распределенные представления слов и фраз и их композиционность. Улучшения в нейросетевых системах обработки информации) за 2013 год](https://arxiv.org/abs/1310.4546)
* [Работа Дую Тенга (Duyu Tang) и его коллег Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification Association for Computational Linguistics (1) (Изучение векторного представления слов, характерных при составлении картины тональности высказываний в Twitter. Общество компьютерной лингвистики (1)) за 2014 год](http://www.aclweb.org/anthology/P14-1146)

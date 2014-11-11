<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Пример машинного обучения Azure: Анализ мнений

*Пример эксперимента, связанный с этой моделью в ML Studio, можно найти в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**. Имя эксперимента:*

    Sample Experiment - Sentiment Classification - Development

## Описание проблемы

Прогнозирование рейтинга обзора продукции. Рейтинги — 1,2,3,4,5. Это проблема порядковой регрессии, которую также можно решать и как проблему регрессии, и как проблему мультиклассовой классификации.

## Данные

Обзоры книг на Amazon, отобранные на сайте Amazon исследователями из UPenn ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). Исходный набор данных включал в себя 975 000 обзоров с рейтингами 1,2,3,4,5. Для ускорения эксперимента мы сузили набор данных до 10 000 обзоров. Все обзоры подготовлены на английском языке. Обзоры были созданы в период с 1997 по 2007 гг.

## Модель

Мы использовали модуль хэширования функций для преобразования английского текста в целочисленные функции. Мы сравнили три модели:

1.  линейная регрессия
2.  порядковая регрессия с помощью двухклассовой логистической регрессии в качестве основного классификатора
3.  мультиклассовая классификация с помощью мультиклассового логистического регрессивного классификатора

## Результат

Алгоритм| Средняя абсолютная погрешность| Среднеквадратическая ошибка
---------|---------|---------|
Порядковая регрессия | 0.82| 1.41|
Линейная регрессия | 1.04| 1.36|
Мультиклассовая классификация | 0.85 | 1.57

На основании этих результатов мы выбрали порядковую регрессивную модель и на ее основе построили веб-службу.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/

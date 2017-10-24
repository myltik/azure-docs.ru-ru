---
title: "Анализ тональности с помощью глубокого обучения в Машинном обучении Azure | Документация Майкрософт"
description: "Эта статья содержит сведения о том, как выполнить анализ тональности с помощью глубокого обучения в Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 39ae2aa7217b45e8fab77f528b27b77a1b1256bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Анализ тональности с помощью глубокого обучения в службе "Машинное обучение Azure"

Анализ тональности — это хорошо известное задание в сфере обработки естественного языка, основной целью которого является определение тональности в заданном наборе текстов. Цель этого решения — использовать CNTK в качестве серверной части для Keras (библиотека на уровне моделей, предоставляющая высокоуровневые стандартные блоки для разработки моделей глубокого обучения) и реализовать анализ тональности на основе обзоров фильмов.

Решение расположено по адресу https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Ссылка на репозиторий коллекции на GitHub

Ниже приведена ссылка на общедоступный репозиторий GitHub:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Обзор варианта использования

В связи со стремительным ростом данных и увеличением количества мобильных устройств клиенты имеют множество возможностей выражать свои эмоции и отношения к чему угодно и когда угодно. Это мнение или "тональность" часто формируется с помощью социальных сетей в виде отзывов, бесед, репостов, отметок "нравится", твитов и т. д. Анализ тональности может пригодиться предприятиям, которые хотят улучшить свои товары и услуги, принимать более взвешенные решения и лучше продвигать свои бренды.

Чтобы воспользоваться преимуществами анализа тональности, предприятия должны иметь возможность анализировать большие объемы неструктурированных данных из социальных сетей для получения ценной практической информации. В этом примере мы разработаем модели глубокого обучения для выполнения анализа тональности обзоров фильмов с помощью AMLWorkbench.

## <a name="prerequisites"></a>Предварительные требования

* [Учетная запись Azure](https://azure.microsoft.com/free/) (доступны бесплатные пробные версии).

* Установленная копия [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Чтобы установить эту программу и создать рабочую область, выполните инструкции из [краткого руководства по установке](./quickstart-installation.md).

* Для ввода в эксплуатацию модели лучше всего, чтобы модуль Docker был установлен и работал локально. Если нет, можно воспользоваться вариантом с кластером. Однако запуск Службы контейнера Azure (ACS) может быть затратным.

* В этом решении предполагается, что Azure Machine Learning Workbench работает в Windows 10 с модулем Docker, установленным на локальном компьютере. В macOS большинство инструкций будут аналогичны.

## <a name="data-description"></a>Описание данных

Для этого примера используется созданный вручную небольшой набор данных, который расположен в [папке данных](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

Первый столбец содержит обзоры фильмов, а второй — их тональность (0 — негативная и 1 — позитивная). Набор данных используется только для демонстрации. Обычно, чтобы получить оценку тональности, необходимо иметь большой набор данных. Например, [модель анализа тональности обзоров фильмов IMDB](https://keras.io/datasets/#datasets ) из Keras состоит из набора данных 25 000 обзоров фильмов из IMDB, которые имеют отметку тональности (позитивную или негативную). Назначение этого практического занятия — показать, как выполнять анализ тональности с помощью глубокого обучения с AMLWorkbench.

## <a name="scenario-structure"></a>Структура сценария

Папки упорядочены следующим образом.

1. Весь код, связанный с выполнением анализа тональности с помощью AMLWorkbench, расположен в корневой папке.
2. data: содержит набор данных, используемых в решении.
3. docs: содержит все практические занятия.

Порядок выполнения практических заданий решения выглядит так:

| Порядок| Имя файла | Связанные файлы |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | data/sampleReviews.txt |
| 2 | [`SentimentAnalysisModelingKerasWithCNTKBackend.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKerasWithCNTKBackend.md) | SentimentExtraction.py |
| 3 | [`SentimentAnalysisModelingDocker.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingDocker.md) | SentimentExtractionDocker.py |
| 4. | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | Operaionalization |

## <a name="conclusion"></a>Заключение

В заключении это решение описывает, как использовать глубокое обучение для выполнения анализа тональности с помощью Azure Machine Learning Workbench. Указанное решение может использовать CNTK или Tensorflow в качестве серверной части Keras. Кроме того, мы также можем ввести в эксплуатацию модели HDF5.

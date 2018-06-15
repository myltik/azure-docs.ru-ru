---
title: Классификация изображений с помощью пакета машинного обучения Azure (AML) для компьютерного зрения (AMLPCV) и командного процесса обработки и анализа данных (TDSP) | Документация Майкрософт
description: В статье описано, как выполнить классификацию изображений с использованием командного процесса обработки и анализа данных и пакета машинного обучения Azure (AML) для компьютерного зрения (AMLPCV)
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837224"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Классификация изображений рака кожи с помощью пакета машинного обучения Azure (AML) для компьютерного зрения (AMLPCV) и командного процесса обработки и анализа данных (TDSP)

## <a name="introduction"></a>Введение

В этой статье вы узнаете, как обучать, тестировать и развертывать модели **классификации изображений** с помощью [пакета машинного обучения Azure для компьютерного зрения](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) (AMLPCV). В этом примере применяется структура и шаблоны TDSP в [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Полный пример см. в этом пошаговом руководстве. Он использует [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) в качестве платформы глубокого обучения, которое выполняется на [виртуальной машине для обработки и анализа данных](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), развернутой на компьютере с GPU. В развертывании используется CLI для ввода Azure ML в эксплуатацию.

Множество задач в области компьютерного зрения можно реализовать, используя классификацию изображений. К ним относятся создание моделей, которые отвечают на такие вопросы, как "Присутствует ли объект на изображении?". Объектом может быть *собака*, *машина* или *корабль*. Это также могут быть более сложные вопросы, такие как: "Какой класс тяжести глазной болезни обнаруживается при сканировании сетчатки пациента?". AMLPCV упрощает процесс обработки и моделирования данных для классификации изображений. 

## <a name="link-to-github-repository"></a>Ссылка на репозиторий GitHub
Сводная документация по этому примеру размещена здесь. Более подробную документацию можно найти на [сайте GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Руководство по командному процессу обработки и анализа данных с помощью AMLPCV

В руководстве используется [жизненный цикл командного процесса обработки и анализа данных (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview).

В рамках этого руководства рассматриваются следующие шаги жизненного цикла:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Получение данных](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Для задачи классификации изображений используется набор данных ISIC. ISIC (Международное сотрудничество в области визуализации кожи) — это партнерство между научными и промышленными организациями для облегчения применения цифровой визуализации кожи для изучения и снижения смертности от меланомы. Архив [ISIC](https://isic-archive.com/#images) содержит более 13 000 изображений повреждений кожи с метками о доброкачественности или злокачественности. Скачаем примеры изображений из архива ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Моделирование](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling).
На этапе моделирования выполняются следующие шаги. 

<b>2.1. Создание набора данных</b><br>

Чтобы создать объект набора данных в AMLPCV, предоставьте корневой каталог изображений на локальном диске. 

<b>2.2. Визуализация изображений и создание заметок</b><br>

Визуализируйте изображения в объекте набора данных и при необходимости исправьте некоторые метки.

<b>2.3. Дополнение изображений</b><br>

Дополните объект набора данных с использованием преобразований, описанных в библиотеке [imgaug](https://github.com/aleju/imgaug).

<b>2.4. Определение модели DNN</b><br>

Определите архитектуру модели, используемую на этапе обучения. В AMLPCV поддерживаются шесть предварительно обученных различных моделей обучения глубинной нейронной сети: AlexNet, Resnet-18, Resnet-34 и Resnet-50, Resnet-101 и Resnet-152.

<b>2.5. Обучение классификатора</b><br>

Обучите нейронные сети со стандартными или настраиваемыми параметрами.

<b>2.6. Оценка и визуализация</b><br>

На этом подэтапе предоставляются функциональные возможности для оценки эффективности обученной модели на независимом тестовом наборе данных. Метрики оценки включают в себя точность, узнавание, полноту, а также кривую ROC.

Эти подэтапы подробно описаны в соответствующей записной книжке Jupyter. Мы также предоставили рекомендации по настройке параметров, таких как скорость обучения, размер мини-пакета и скорость отсева, для дальнейшего улучшения эффективности модели.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Развертывание](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment).

Этот шаг вводит в эксплуатацию модель, полученную на этапе моделирования. В нем приводятся предварительные условия введения в эксплуатацию и настройка. Здесь также рассматривается использование веб-службы. В этом руководстве вы можете научиться создавать модели глубокого обучения с помощью AMLPCV и вводить модель в эксплуатацию в Azure.

## <a name="next-steps"></a>Дальнейшие действия
Прочтите дополнительную документацию по работе с [пакетом машинного обучения Azure для компьютерного зрения (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) и [командным процессом обработки и анализа данных (TDSP)](https://aka.ms/tdsp), чтобы приступить к работе.


## <a name="references"></a>Ссылки

* [Пакет машинного обучения Azure для компьютерного зрения (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation).
* [Виртуальная машина для обработки и анализа данных](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)


---
title: Пакеты Python для службы "Машинное обучение Azure"
description: Сведения о пакетах Python, доступных для пользователей службы "Машинное обучение Azure".
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7a8f9e9d757aaab04706dd3b7dc2b962b3a04130
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833273"
---
# <a name="python-packages-for-azure-machine-learning"></a>Пакеты Python для службы "Машинное обучение Azure"

Информация о собственных пакетах Python корпорации Майкрософт для службы "Машинное обучение Azure". Эти библиотеки и функции можно использовать в сочетании с другими пакетами с открытым кодом или пакетами сторонних разработчиков, но чтобы использовать собственные пакеты, ваш код Python должен выполняться в службе или на компьютере, который предоставляется интерпретаторами.

Пакеты службы "Машинное обучение Azure" — это **устанавливаемые расширения pip на Python для службы "Машинное обучение Azure"**, позволяющие специалистам по анализу данных и разработчикам систем ИИ быстро создать и развернуть модели машинного и глубокого обучения для различных доменов.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Пакет службы "Машинное обучение Azure" для компьютерного зрения

С помощью пакета "Машинное обучение Azure" для компьютерного зрения можно создать, настроить и развернуть модели глубокого обучения для классификации изображений, обнаружения объектов и сходства изображений.

Выполните следующие шаги для этого пакета:
1. [Скачайте](https://aka.ms/aml-packages/vision/download) пакет.
1. Ознакомьтесь [с документацией по установке](https://aka.ms/aml-packages/vision).
1. [Создайте и разверните модель компьютерного зрения](how-to-build-deploy-image-classification-models.md) с помощью Jupyter Notebook.
1. Посмотрите [справочную документацию по пакету](https://aka.ms/aml-packages/vision).

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Пакет службы "Машинное обучение Azure" для прогнозирования

С помощью пакета службы "Машинное обучение Azure" вы можете создать и развернуть модели прогнозирования временных рядов для сценариев прогнозирования финансов и спроса.

Выполните следующие шаги для этого пакета:
1. [Скачайте](https://aka.ms/aml-packages/forecasting/download) пакет.
1. Ознакомьтесь [с документацией по установке](https://aka.ms/aml-packages/forecasting).
1. [Создайте и разверните модель прогнозирования](how-to-build-deploy-forecast-models.md) с помощью Jupyter Notebook.
1. Посмотрите [справочную документацию по пакету](https://aka.ms/aml-packages/forecasting).

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Пакет службы "Машинное обучение Azure" для анализа текста

С помощью пакета службы "Машинное обучение Azure" для анализа текста можно создать модели глубокого обучения по тексту для классификации текста, извлечения пользовательского объекта и внедрения слов.

Выполните следующие шаги для этого пакета:
1. [Скачайте](https://aka.ms/aml-packages/text/download) пакет.
1. Ознакомьтесь [с документацией по установке](https://aka.ms/aml-packages/text).
1. [Создайте и разверните модели классификации текста](how-to-build-deploy-text-classification-models.md) с помощью Jupyter Notebook.
1. Посмотрите [справочную документацию по пакету](https://aka.ms/aml-packages/text).

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (ускорение FPGA)

С помощью пакета службы "Машинное обучение Azure" для аппаратного ускорения специалисты по анализу данных и разработчики систем ИИ могут создавать признаки изображений с помощью квантованной версии ResNet 50, обучать классификаторы на основе этих признаков, а затем развернуть модели в [программируемых пользователем вентильных матрицах (FPGA)](concept-accelerate-with-fpgas.md) в Azure для обеспечения сверхнизкой задержки.

Выполните следующие шаги для этого пакета:
1. [Скачайте](https://aka.ms/aml-real-time-ai-package) пакет.
1. Ознакомьтесь [с документацией по установке](reference-fpga-package-overview.md).
1. [Разверните модель в FPGA](how-to-deploy-fpga-web-service.md).


---
title: Служба "Машинное обучение Azure" и программируемые пользователем вентильные матрицы
description: Узнайте, как ускорить модели и глубокие нейронные сети с помощью FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Сведения о программируемых пользователем вентильных матрицах

Программируемые пользователем вентильные матрицы (FPGA) — это перенастраиваемые интегральные микросхемы. Их можно изменять и внедрять новую логику. Аппаратно-ускоренные модели службы "Машинное обучение Azure" позволяют развертывать обученные модели FPGA в облаке Azure.

Эта функция работает на базе технологии Project Brainwave, которая обрабатывает преобразование глубоких нейронных сетей (DNN) в программу FPGA. 

## <a name="why-use-an-fpga"></a>Причины использования FPGA

Матрицы FPGA отличаются сверхнизкой задержкой, а также оптимизированы под оценку данных в пакетах размера 1 (требований к большому размеру пакета нет).  Кроме того, эти матрицы экономичны и доступны в Azure.  Технология Project Brainwave позволяет параллелизовать предварительно обученные глубокие нейронные сети через матрицы FPGA, что обеспечивает возможности масштабирования службы.

## <a name="next-steps"></a>Дополнительная информация

[Развертывание модели как веб-службы в FPGA с помощью службы "Машинное обучение Azure"](how-to-deploy-fpga-web-service.md)

[Azure Machine Learning Hardware Acceleration package](reference-fpga-package-overview.md) (Пакет аппаратного ускорения службы "Машинное обучение Azure")
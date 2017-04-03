---
title: "Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive | Документация Майкрософт"
description: "Изучение данных, хранящихся в различных средах Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: c046220304075242f655bced2f0f16a6fb692ad3
ms.lasthandoff: 02/11/2017


---
# <a name="heading"></a>Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive
Этот документ содержит ссылки на статьи, рассказывающие, как получить выборку данных из одного из трех расположений Azure.

* **данных контейнера больших двоичных объектов Azure** осуществляется путем их программного скачивания и последующей выборки с помощью примера кода на языке Python.
* **данных SQL Server** осуществляется с помощью как SQL, так и языка программирования Python. 
* **данных таблицы Hive** осуществляется с помощью запросов Hive.

**Меню** ниже содержит ссылки на разделы, в которых описан процесс выборки данных из каждой среды хранения Azure. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="why-sample-data"></a>Для чего нужна выборка данных?
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.



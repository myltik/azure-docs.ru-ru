---
title: Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive | Документация Майкрософт
description: Изучение данных, хранящихся в различных средах Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 0efd754936b67611a747c6c5756de92443a937e4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838005"
---
# <a name="heading"></a>Выборка данных в контейнерах больших двоичных объектов Azure, SQL Server и таблицах Hive
Этот документ содержит ссылки на статьи, в которых объясняется, как получить выборку данных из одного из трех расположений Azure.

* **данных контейнера больших двоичных объектов Azure** осуществляется путем их программного скачивания и последующей выборки с помощью примера кода на языке Python.
* **данных SQL Server** осуществляется с помощью как SQL, так и языка программирования Python. 
* **данных таблицы Hive** осуществляется с помощью запросов Hive.

**Меню** ниже содержит ссылки на разделы, в которых описан процесс выборки данных из каждой среды хранения Azure. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Для чего нужна выборка данных?**

Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.


---
title: "Загрузка данных в среды службы хранилища Azure для аналитики | Документация Майкрософт"
description: "Перемещение данных в хранилище больших двоичных объектов Azure и из него"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 3a4e8214043ece060cfabae8f74ebf21344777d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Загрузка данных в среды хранения для аналитики
Процесс обработки и анализа данных группы требует приема или загрузки данных в различные среды хранения для обработки или анализа наиболее подходящим способом на каждом этапе процесса. Для обработки обычно используются следующие места хранения данных: хранилище больших двоичных объектов Azure, базы данных SQL Azure, SQL Server на виртуальной машине Azure, HDInsight (Hadoop) и Машинное обучение Azure. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Это **меню** содержит ссылки на статьи, описывающие прием данных в эти целевые среды, где они могут храниться и обрабатываться.

Целевые среды, в которые требуется принять данные, чтобы достичь целей анализа определят технические требования и потребности бизнеса, а также исходное расположение, формат и размер данных. Нередки сценарии, когда требуется перемещать данные между несколькими средами для выполнения различных задач, необходимых для построения прогнозирующей модели. Эта последовательность задач может включать в себя, например, просмотр данных, предварительную обработку, очистку, понижение частоты выборки и обучение модели.


---
title: Импорт данных из другого эксперимента в Студию машинного обучения | Документация Майкрософт
description: В статье описывается, как сохранить учебные данные в студию машинного обучения Azure и использовать их в другом эксперименте.
keywords: импорт данных, данные, источники данных, обучающие данные
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev
ms.openlocfilehash: 8279b13270d4bba990e6670acd714be19118e98c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834834"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Импорт данных в студию машинного обучения Azure из другой среды
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Иногда понадобится получить в эксперименте промежуточный результат, который будет использоваться в другом эксперименте. Для этого сохраните модуль как набор данных, выполнив указанные ниже действия.

1. Щелкните выходные данные модуля, которые требуется сохранить в виде набора данных.
2. Щелкните **Сохранить как набор данных**.
3. При появлении запроса введите имя и описание, которое позволит легко идентифицировать набор данных.
4. Установите флажок **ОК** .

После завершения сохранения набор данных будет доступен для использования в любом эксперименте в рабочей области. Его можно найти в списке **Сохраненные наборы данных** в палитре модулей.


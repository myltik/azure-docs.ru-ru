---
title: "Импорт данных из другого эксперимента в Студию машинного обучения | Документация Майкрософт"
description: "В статье описывается, как сохранить учебные данные в студию машинного обучения Azure и использовать их в другом эксперименте."
keywords: "импорт данных, данные, источники данных, обучающие данные"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 1738899a271045e37cdcedaaf862553f29ec92f1
ms.openlocfilehash: b1c06135cf8628197fb9601affd6a7131b41d0ca
ms.lasthandoff: 12/19/2016


---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Импорт данных в студию машинного обучения Azure из другой среды
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Иногда понадобится получить в эксперименте промежуточный результат, который будет использоваться в другом эксперименте. Для этого сохраните модуль как набор данных, выполнив указанные ниже действия.

1. Щелкните выходные данные модуля, которые требуется сохранить в виде набора данных.
2. Щелкните **Сохранить как набор данных**.
3. При появлении запроса введите имя и описание, которое позволит легко идентифицировать набор данных.
4. Установите флажок **ОК** .

После завершения сохранения набор данных будет доступен для использования в любом эксперименте в рабочей области. Его можно найти в списке **Сохраненные наборы данных** в палитре модулей.



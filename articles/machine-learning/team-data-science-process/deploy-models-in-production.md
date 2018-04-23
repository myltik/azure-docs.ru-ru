---
title: Развертывание моделей в рабочей среде | Документация Майкрософт
description: Способы развертывания моделей в рабочей среде для их использовании при принятии бизнес-решений.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: bradsev
ms.openlocfilehash: 681f227a04802c4e27e42083c1eb63f620a8fc77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-models-in-production"></a>Развертывание моделей в рабочей среде

Развертывание в рабочей среде позволяет использовать модель как важный элемент в работе организации. Прогнозы, полученные от развернутой модели можно использовать для принятия бизнес-решений.

## <a name="production-platforms"></a>Рабочие платформы
Поместить модели в рабочую среду можно с использованием разных подходов и платформ. Вот некоторые из них:


- [Развертывание модели в службе "Машинное обучение Azure"](../desktop-workbench/model-management-overview.md)
- [Развертывание модели на сервере SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>Перед развертыванием необходимо убедиться, что показатели задержки модели достаточно малы для использования в рабочей среде.
>


>[!NOTE]
>Разработка с помощью Студии машинного обучения Azure описывается в разделе [Развертывание веб-службы машинного обучения Azure](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-тестирование
Если в рабочей среде используется несколько моделей, удобно выполнять [A/B-тестирование](https://en.wikipedia.org/wiki/A/B_testing), чтобы сравнить их эффективность. 

 
## <a name="next-steps"></a>Дополнительная информация

Также доступны пошаговые руководства, которые демонстрируют все этапы процесса для **конкретных сценариев**. Эти этапы с иллюстрациями и краткими описаниями перечислены в статье [Пошаговые руководства по процессу обработки и анализа данных группы](walkthroughs.md). В них показано, как объединить облачные и локальные средства и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение. 
 



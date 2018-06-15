---
title: Развертывание моделей в рабочей среде | Документация Майкрософт
description: Способы развертывания моделей в рабочей среде для их использовании при принятии бизнес-решений.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: deguhath
ms.openlocfilehash: 0505d58261ec32015f8847b710791249f87f049b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838298"
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
 



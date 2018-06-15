---
title: Новые возможности в Машинном обучении Azure | Документация Майкрософт
description: Новые возможности в Машинном обучении Azure.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 61bea7fde96b239a50ec25a702a73ecfb62ce717
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836078"
---
# <a name="whats-new-in-azure-machine-learning"></a>Новые возможности Машинного обучения Azure

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Обновления для Машинного обучения Microsoft Azure за март 2017 года обеспечивают следующее.



* Выделенная емкость для заданий службы выполнения пакетов (BES) машинного обучения Azure

    Для обработки пул пакетной службы машинного обучения использует [пакетную службу Azure](../../batch/batch-technical-overview.md), чтобы предоставить пользователям управление масштабированием службы выполнения пакетов машинного обучения Azure. Обработка в пуле пакетной службы дает возможность создавать пулы пакетной службы Azure, в которые можно отправлять пакетные задания и выполнять их предсказуемым образом.

    Дополнительные сведения см. в разделе [Пакетные службы Azure для обработки заданий машинного обучения](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Обновления для машинного обучения Microsoft Azure за август 2016 года обеспечивают приведенные ниже возможности.
* Классическими веб-службами теперь можно управлять на новом портале [веб-служб машинного обучения Microsoft Azure](https://services.azureml.net/), который предоставляет возможность централизованно управлять всеми аспектами веб-службы.    
  * На нем предоставляется [статистика использования](manage-new-webservice.md) веб-службы.
  * Упрощается тестирование вызовов удаленных запросов машинного обучения Azure с использованием примеров данных.
  * Предоставляется новая страница тестирования службы пакетного выполнения, на которой можно использовать примеры данных и журнал отправки заданий.
  * Обеспечивается более простое управление конечными точками.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Обновления для машинного обучения Microsoft Azure за июль 2016 года обеспечивают приведенные ниже возможности.
* Веб-службы теперь управляются как ресурсы Azure, с помощью интерфейсов [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , что обеспечивает следующие усовершенствования:
  * Добавлены новые [интерфейсы REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) для развертывания веб-служб с помощью Resource Manager и управления ими.
  * Доступен новый портал [веб-служб машинного обучения Microsoft Azure](https://services.azureml.net/), который позволяет централизованно управлять всеми аспектами веб-службы.
* Добавлена новая модель развертывания веб-служб в нескольких регионах по подписке, в которой применяются интерфейсы API на основе Resource Manager, использующие поставщик ресурсов Resource Manager для веб-служб.
* Добавлены новые [ценовые планы](https://azure.microsoft.com/pricing/details/machine-learning/) и возможности планирования управления, использующие новые функции поставщика ресурсов Resource Manager для выставления счетов.
  * Теперь можно [развернуть веб-службу в нескольких регионах](how-to-deploy-to-multiple-regions.md) без необходимости создавать новую подписку в каждом из них.
* Предоставляется [статистика использования](manage-new-webservice.md)веб-службы.
* Упрощается тестирование вызовов удаленных запросов машинного обучения Azure с использованием примеров данных.
* Предоставляется новая страница тестирования службы пакетного выполнения, на которой можно использовать примеры данных и журнал отправки заданий.

Кроме того, обновлена студия машинного обучения. Теперь вы можете развертывать веб-службы с помощью новой модели или продолжать использовать классическую модель развертывания. 


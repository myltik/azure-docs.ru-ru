---
title: "Аналитика Azure для государственных организаций | Документация Майкрософт"
description: "Данное руководство включает сравнительный анализ характеристик и рекомендации по разработке приложений для разработчиков Azure."
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Аналитика Azure для государственных организаций
В этой статье описаны службы аналитики, важные отличия и рекомендации для среды Azure для государственных организаций.

## <a name="hdinsight"></a>HDInsight
В Azure для государственных организаций используется общедоступная версия HDInsight на платформе Linux Standard. В <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>этом видеоролике</a> демонстрируется, как с помощью HDInsight создавать решения, нацеленные на обработку данных, в Azure для государственных организаций.

Выпуск HDInsight на платформе Linux Premium ожидается в ближайшее время.

### <a name="variations"></a>Варианты
Приведенные ниже возможности HDInsight сейчас не доступны в Azure для государственных организаций.

* HDInsight не доступен для Windows.
* Azure Data Lake Store в настоящее время не доступна в Azure для государственных организаций. Хранилище BLOB-объектов Azure сейчас является единственным доступным вариантом хранилища.

URL-адреса для Log Analytics отличаются в Azure для государственных организаций:

| Тип службы | Azure Public | Azure Government |
| --- | --- | --- |
| Кластер HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Дополнительные сведения см. в [общедоступной документации по HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).

## <a name="power-bi"></a>Power BI
Общедоступная версия Power BI US Government входит в состав подписок Office 365 US Government Community. Дополнительные сведения о Power BI US Government можно просмотреть <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>здесь</a>. В <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>этом видеоролике</a> демонстрируется, как с помощью Power BI создавать решения, нацеленные на обработку данных, в Azure для государственных организаций.

Выпуск Power BI Embedded ожидается в ближайшее время.

### <a name="variations"></a>Варианты

URL-адреса для Power BI в версии US Government отличаются:

| Тип службы | Сообщество по Power BIPower BI Commercial | Power BI US Government |
| --- | --- | --- |
| URL-адрес Power BI | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на <a href="https://blogs.msdn.microsoft.com/azuregov/">блог Microsoft Azure для государственных организаций. </a>


---
title: "Рекомендации Azure Advisor по производительности | Документация Майкрософт"
description: "Используйте Advisor для оптимизации производительности развернутых служб Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: c350134e8cea5771900e2e88b02b572c20e9d5bd

---

# <a name="advisor-performance-recommendations"></a>Рекомендации Azure Advisor по производительности

Рекомендации Advisor по производительности помогают улучшить работу, повысить производительность и увеличить скорость реагирования критически важных бизнес-приложений. Получить рекомендации по производительности от Advisor можно на вкладке **Производительность** панели мониторинга Advisor.

![Вкладка "Производительность" в Advisor](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>Рекомендации по базе данных SQL

Advisor предоставляет согласованное сводное представление с рекомендациями для всех ресурсов Azure. Advisor интегрируется с Помощником по базам данных SQL, чтобы вы могли получить рекомендации по повышению производительности базы данных SQL Azure. Помощник по базам данных SQL оценивает производительность ваших баз данных SQL, анализируя журнал использования. После этого он предлагает рекомендации, наиболее подходящие для типовых нагрузок в вашей базе данных. 

> [!NOTE]
> Чтобы рекомендации сформировались, база данных должна постоянно использоваться на протяжении примерно одной недели. Помощник по базам данных SQL обеспечивает более эффективную оптимизацию при стабильном потоке запросов, чем на основе случайных всплесков активности.

Дополнительные сведения о Помощнике по базам данных SQL см. в разделе [Помощник по работе с базами данных SQL](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Рекомендации по базе данных SQL](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Рекомендации по кэшу Redis

Azure Advisor выявляет экземпляры кэша Redis, на производительность которых может отрицательно влиять использование большого объема памяти, нагрузка на сервер, пропускная способность сети или большое количество клиентских подключений. Он также предоставляет практические рекомендации, позволяющие избежать возможных проблем. Дополнительные сведения о рекомендациях по кэшу Redis см. в разделе [Помощник по кэшу Redis](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="app-services-recommendations"></a>Рекомендации по службам приложений

Azure Advisor объединяет практические рекомендации по улучшению работы службы приложений и обнаружению соответствующих возможностей платформы. Ниже приведены примеры рекомендаций по службам приложений:
- обнаружение экземпляров, исчерпавших ресурсы памяти или ЦП во время выполнения приложений, и описание вариантов устранения;
- обнаружение экземпляров, для которых совместное размещение ресурсов, например веб-приложений и баз данных, может повысить производительность и снизить стоимость. 

Дополнительные сведения о рекомендациях по службам приложений см. в разделе [Рекомендации по использованию службы приложений Azure](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Рекомендации по службам приложений](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Как получить доступ к рекомендации по производительности в Advisor

1. Войдите на [портал Azure](https://portal.azure.com).
2. В области навигации слева щелкните **Больше служб**, прокрутите вниз область меню служб до раздела **Мониторинг и управление** и щелкните **Помощник по Azure**. Откроется панель мониторинга Advisor. 
3. На панели мониторинга Advisor щелкните вкладку **Производительность** и выберите подписку, для которой вы хотите получать рекомендации.

> [!NOTE]
> Advisor создает рекомендации для подписок, для которых вам назначена роль **владельца, участника или читателя**.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о рекомендациях Advisor, ознакомьтесь с приведенными ниже материалами.

-  [Общие сведения об Azure Advisor](advisor-overview.md)
-  [Приступая к работе с Azure Advisor](advisor-get-started.md)
-  [Рекомендации Azure Advisor по высокой доступности](advisor-high-availability-recommendations.md)
-  [Рекомендации Azure Advisor по безопасности](advisor-security-recommendations.md)
-  [Рекомендации Azure Advisor по затратам](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->



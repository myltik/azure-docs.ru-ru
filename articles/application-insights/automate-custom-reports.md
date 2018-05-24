---
title: Автоматизация настраиваемых отчетов с использованием данных Azure Application Insights
description: Автоматизация настраиваемых ежедневных, еженедельных или ежемесячных отчетов с использованием данных Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072661"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Автоматизация настраиваемых отчетов с использованием данных Azure Application Insights

Благодаря периодическим отчетам команды располагают актуальной информацией о состоянии функционирования критически важных бизнес-служб. Автоматизированные отчеты с надежной доставкой необходимых данных позволяют повысить эффективность труда разработчиков, команд DevOps/SRE и их руководителей. При этом входить на портал для получения сведений не требуется. Эти отчеты также помогают определить постепенный рост частоты задержек, нагрузок или сбоев, подчас не приводящий к запуску правил оповещения.

В каждой организации есть свои уникальные потребности в отчетности, например следующие: 

* определенная процентильная информация по метрикам или настраиваемым метрикам в отчете;
* наличие различных отчетов для ежедневных, еженедельных и ежемесячных сверток данных для различных аудиторий;
* сегментация по настраиваемым атрибутам, таким как регион или среда; 
* объединение некоторых ресурсов AI в одном отчете, даже если они могут находиться в разных подписках или группах ресурсов и т. д.;
* разделение отчетов, содержащих важные метрики, отправляемые выборочной аудитории;
* отчеты для заинтересованных лиц, у которых может отсутствовать доступ к ресурсам портала.

> [!NOTE] 
> Сообщение электронной почты еженедельного дайджеста Application Insights не поддерживало настройки, поэтому вместо него будут использоваться настраиваемые варианты, приведенные ниже. Последнее сообщение электронной почты еженедельного дайджеста будет отправлено 11 июня 2018 г. Настройте один из следующих параметров, чтобы получать аналогичные настраиваемые отчеты (используйте предложенный ниже запрос).

## <a name="to-automate-custom-report-emails"></a>Автоматизация сообщений электронной почты с настраиваемыми отчетами

Для создания настраиваемых отчетов по расписанию можно [программно запрашивать данные Application Insights](https://dev.applicationinsights.io/). Указанные ниже варианты помогут быстро приступить к работе.

* [Автоматизация отчетов с помощью Microsoft Flow](app-insights-automate-with-flow.md)
* [Автоматизация отчетов с помощью Logic Apps](automate-with-logic-apps.md)
* В сценарии мониторинга используйте [функцию Azure](https://azure.microsoft.com/services/functions/) "Дайджест по расписанию AppInsights". Для доставки сообщения электронной почты эта функция использует SendGrid. 

    ![Шаблон функции Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Пример запроса для сообщения электронной почты еженедельного дайджеста
В следующем запросе показано объединение нескольких наборов данных для формирования отчета, аналогичного сообщению электронной почты еженедельного дайджеста. При необходимости настройте его и используйте с любым приведенным выше вариантом для автоматизации еженедельного отчета.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о создании [запросов Analytics](app-insights-analytics-using.md).
- Дополнительные сведения о [программных запросах к данным Application Insights](https://dev.applicationinsights.io/).
- Дополнительные сведения о [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).



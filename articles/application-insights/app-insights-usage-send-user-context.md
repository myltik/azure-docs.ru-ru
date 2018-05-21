---
title: Отправка идентификаторов контекста пользователей для обеспечения оптимального использования в Azure Application Insights | Документация Майкрософт
description: Отслеживайте, как пользователи перемещаются по службе, назначив каждому из них строку уникального постоянного идентификатора в Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin;abgreg
ms.openlocfilehash: 196eeb7b5a817ff932f99c7db86ead5625b5f206
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Отправка идентификаторов контекста пользователей для обеспечения оптимальной работы в Azure Application Insights

## <a name="tracking-users"></a>Отслеживание пользователей

Application Insights позволяет отслеживать пользователей с помощью набора инструментов для использования продуктов: 
* [Пользователи, сеансы, события](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Воронки](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Сохранение](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* когорты;
* [Книги](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Для отслеживания действий пользователя за определенный период Application Insights требуется идентификатор для каждого пользователя или сеанса. Добавьте приведенные идентификаторы в каждое пользовательское событие или представление страницы.
- Для пользователей, воронок, инструментов хранения и когорт: включите идентификатор пользователя.
- Для сеансов: включите идентификатор сеанса.

Если приложение интегрировано с [пакетом SDK для JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), идентификатор пользователя отслеживается автоматически.

## <a name="choosing-user-ids"></a>Выбор идентификаторов пользователей

Идентификаторы пользователей должны сохраняться в пользовательских сеансах, чтобы можно было отследить поведение пользователей за период времени. Существуют различные подходы к сохранению идентификатора.
- Определение пользователя, уже имеющееся в службе.
- Если служба имеет доступ к браузеру, она может передать ему файл cookie с идентификатором. Идентификатор хранится, пока файл cookie остается в браузере пользователя.
- При необходимости можно использовать новый идентификатор для каждого сеанса, но результаты для пользователей будут ограничены. Например, вы не сможете узнать, как поведение пользователя меняется со временем.

Идентификатор должен быть идентификатором GUID или другой строкой, достаточно сложной для уникальной идентификации каждого пользователя. Например, это может быть длинное случайное число.

Если идентификатор содержит персональные идентификационные сведения о пользователе, его не следует отправлять в качестве идентификатора пользователя в Application Insights. Такой идентификатор можно отправить как [аутентифицированный идентификатор пользователя](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users). Однако такой идентификатор не удовлетворяет требования к идентификатору пользователя для сценариев использования.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Приложения ASP.NET: установка контекста пользователя в ITelemetryInitializer

Создайте инициализатор телеметрии, как подробно описано [здесь](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), и задайте Context.User.Id и Context.Session.Id.

В этом примере в качестве идентификатора пользователя задается идентификатор, срок действия которого истекает по завершении сеанса. По возможности используйте идентификатор пользователя, который сохраняется между сеансами.

```csharp

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Дополнительная информация
- Чтобы обеспечить оптимальное использование, начните отправлять [пользовательские события](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) или [сведения о просмотрах страниц](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Если вы уже сделали это, изучите инструменты использования, чтобы узнать, как пользователи используют службу.
    * [Общие сведения об использовании](app-insights-usage-overview.md)
    * [Анализ пользователей, сеансов и событий в Application Insights](app-insights-usage-segmentation.md)
    * [Воронки](usage-funnels.md)
    * [Сохранение](app-insights-usage-retention.md)
    * [Книги](app-insights-usage-workbooks.md)

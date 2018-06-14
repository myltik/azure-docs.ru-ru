---
title: Поиск по ресурсам с помощью Azure Log Analytics | Документация Майкрософт
description: Из этой статьи вы узнаете, как выполнять запросы к ресурсам из нескольких рабочих областей и приложения App Insights в вашей подписке.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: magoedte
ms.openlocfilehash: e46ae3af3a718703f9e1d6b847b2342469bf3a1e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517229"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Выполнение поиска по журналам нескольких ресурсов в Log Analytics  

Ранее с помощью службы Azure Log Analytics можно было анализировать данные только в пределах текущей рабочей области. Это препятствовало выполнению запросов в нескольких рабочих областях, определенных вашей подпиской.  Кроме того, элементы телеметрии, полученные из веб-приложения с помощью Application Insights, можно было искать только непосредственно в Application Insights или из Visual Studio.  Это также усложняло встроенный совместный анализ операционных данных и данных приложения.   

Теперь вы можете выполнять запросы не только в нескольких рабочих областях Log Analytics, но также запрашивать данные из приложения Application Insights в той же или другой группе ресурсов или в другой подписке. Благодаря этому вы можете получить представление данных на уровне системы.  Запросы таких типов можно выполнять только на [расширенном портале](log-analytics-log-search-portals.md#advanced-analytics-portal), а не на портале Аzure.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Выполнение запросов в рабочих областях Log Analytics и запрос данных из приложения Application Insights
Воспользуйтесь идентификатором [*рабочей области*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) для обращения к другой рабочей области в запросе, а для Application Insights используйте идентификатор [*приложения*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

### <a name="identifying-workspace-resources"></a>Определение ресурсов рабочей области
В приведенных ниже примерах демонстрируются запросы к различным рабочим областям Log Analytics. Эти запросы предназначены для получения итогового количества обновлений из таблицы "Обновление" в рабочей области с именем *contosoretail-it*. 

Определить рабочую область можно несколькими способами:

* Имя ресурса — это имя рабочей области в удобном для восприятия формате, которое иногда называется *именем компонента*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >При определении рабочей области по имени предполагается его уникальность во всех доступных подписках. Если у вас есть несколько приложений с таким именем, запрос не выполнится из-за неоднозначности. В этом случае необходимо воспользоваться другим идентификатором.

* Полное имя — это имя рабочей области, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор рабочей области — это уникальный, неизменяемый идентификатор, присвоенный каждой рабочей области и представленный в виде глобального уникального идентификатора (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Идентификатор ресурса Azure — это уникальный идентификатор рабочей области в Azure. Он используется, если имя ресурса является неоднозначным.  Для рабочих областей используется следующий формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Например: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Определение приложения
В приведенных ниже примерах возвращается итоговое количество запросов к приложению *fabrikamapp* в Application Insights. 

Определение приложения в Application Insights можно обеспечить с помощью выражения *app(Identifier)*.  Аргумент *Identifier* указывает приложение с помощью одного из следующих объектов:

* Имя ресурса — это имя приложения в удобном для восприятия формате, которое иногда называется *именем компонента*.  

    `app("fabrikamapp")`

* Полное имя — имя приложения, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор — глобальный уникальный идентификатор приложения.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Идентификатор ресурса Azure — это уникальный идентификатор приложения в Azure. Он используется, если имя ресурса является неоднозначным. Используется такой формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Компоненты/OperationalInsights/componentName*.  

    Например: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Дополнительная информация

Полный набор вариантов синтаксиса запросов, доступных в службе Log Analytics, см. в статье [Query Language](https://docs.loganalytics.io/docs/Language-Reference) (Язык запросов).    
---
title: "Выполнение запросов в рабочих областях службы Azure Log Analytics | Документация Майкрософт"
description: "В этой статье показано, как можно выполнять запросы в нескольких рабочих областях и приложении App Insights в вашей подписке."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>Выполнение запросов в нескольких рабочих областях службы Log Analytics

Ранее с помощью службы Azure Log Analytics можно было анализировать данные только в пределах текущей рабочей области, что препятствовало выполнению запросов в нескольких рабочих областях, определенных вашей подпиской.  

Теперь вы можете выполнять запросы не только в нескольких рабочих областях Log Analytics, но также запрашивать данные из приложения Application Insights в той же или другой группе ресурсов или в другой подписке. Благодаря этому вы можете получить представление данных на уровне системы.  Запросы такого типа можно выполнять только на [расширенном портале](log-analytics-log-search-portals.md#advanced-analytics-portal), а не на портале Аzure.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Выполнение запросов в рабочих областях Log Analytics и запрос данных из приложения Application Insights
Воспользуйтесь идентификатором [*рабочей области*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) для обращения к другой рабочей области в запросе, а для Application Insights используйте идентификатор [*приложения*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

Например, первый запрос возвращает из таблицы "Обновление" текущей рабочей области и рабочей области *contosoretail-it* итоговое количество обновлений, которые нужны для классификации.  Во втором примере запрос возвращает итоговое количество запросов к приложению *fabrikamapp* в Application Insights. 

### <a name="identifying-workspace-resources"></a>Определение ресурсов рабочей области
Определение рабочей области можно осуществить несколькими способами:

* Имя ресурса — это имя рабочей области в удобном для восприятия формате, которое иногда называется *именем компонента*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Для определения рабочей области по имени оно должно быть уникальным во всех имеющихся подписках. Если у вас есть несколько приложений с таким именем, запрос не выполнится из-за неоднозначности. В этом случае необходимо воспользоваться другим идентификатором.

* Полное имя — это имя рабочей области, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор рабочей области — это уникальный, неизменяемый идентификатор, присвоенный каждой рабочей области и представленный в виде глобального уникального идентификатора (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Идентификатор ресурса Azure — это уникальный идентификатор рабочей области в Azure. Он используется, если имя ресурса является неоднозначным.  Для рабочих областей используется следующий формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Например: 
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Определение приложения

Определение приложения в Application Insights можно выполнить с помощью выражения *app(Identifier)*.  Аргумент *Identifier* указывает приложение с помощью одного из следующих объектов:

* Имя ресурса — это имя приложения в удобном для восприятия формате, которое иногда называется *именем компонента*.  

    `app("fabrikamapp")`

* Полное имя — имя приложения, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор — глобальный уникальный идентификатор приложения.

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* Идентификатор ресурса Azure — это уникальный идентификатор приложения в Azure. Он используется, если имя ресурса является неоднозначным. Используется такой формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Компоненты/OperationalInsights/componentName*.  

    Например: 
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Дополнительная информация

Полный набор вариантов синтаксиса запросов, доступных в службе Log Analytics, см. в статье [Query Language](https://docs.loganalytics.io/docs/Language-Reference) (Язык запросов).    
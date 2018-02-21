---
title: "Выполнение запросов в рабочих областях службы Azure Log Analytics | Документация Майкрософт"
description: "В следующих примерах этой статьи показано, как можно выполнять запросы в нескольких рабочих областях вашей подписки."
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
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Выполнение запросов в нескольких рабочих областях службы Log Analytics

Ранее с помощью службы Azure Log Analytics можно было анализировать данные только в пределах текущей рабочей области, что препятствовало выполнению запросов в нескольких рабочих областях, определенных вашей подпиской.  

Теперь вам доступна такая возможность, благодаря которой можно получить представление данных на уровне системы.  Запросы такого типа можно выполнять только на [расширенном портале](log-analytics-log-search-portals.md#advanced-analytics-portal), а не на портале Аzure.  

## <a name="querying-across-log-analytics-workspaces"></a>Выполнение запросов в рабочих областях службы Log Analytics
Воспользуйтесь идентификатором *рабочей области* для обращения к другой рабочей области в запросе.  Например, следующий запрос возвращает из таблицы "Обновление" текущей рабочей области и рабочей области *contosoretail-it* итоговое количество обновлений, которые нужны для классификации.  


## <a name="identifying-resources"></a>Определение ресурсов
Определение рабочей области можно осуществить несколькими способами:

* Имя ресурса — это имя рабочей области в удобном для восприятия формате, которое иногда называется *именем компонента*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Для определения рабочей области по имени оно должно быть уникальным во всех имеющихся подписках. Если у вас есть несколько приложений с таким именем, запрос не выполнится из-за неоднозначности. В этом случае необходимо воспользоваться другим идентификатором.

* Полное имя рабочей области, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор рабочей области — это уникальный, неизменяемый идентификатор, присвоенный каждой рабочей области и представленный в виде глобального уникального идентификатора (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Идентификатор ресурса Azure — это уникальный идентификатор рабочей области в Azure. Он используется, если имя ресурса является неоднозначным.  Для рабочих областей используется следующий формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Например: 
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Дополнительная информация

Полный набор вариантов синтаксиса запросов, доступных в службе Log Analytics, см. в статье [Query Language](https://docs.loganalytics.io/docs/Language-Reference) (Язык запросов).    
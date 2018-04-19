---
title: Как выполнить проверку доступа в приложении "Управление привилегированными пользователями" для ресурсов Azure | Документация Майкрософт
description: Из этого документа вы узнаете, как выполнить проверку доступа в PIM для ресурсов Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>Информационные панели ресурсов

Информационная панель представления администратора содержит четыре основных компонента. Графическое представление активаций ролей ресурсов за последние семь дней. Эти данные привязаны к выбранному ресурсу и отображают активацию для наиболее распространенных ролей (владелец, участник, администратор доступа пользователей) и всех объединенных ролей.

Справа от графика активации представлены две диаграммы, отображающие распределение назначений ролей по типу назначения для пользователей и групп. При выборе фрагмента диаграммы изменяется значение в процентах (или наоборот).

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

Под диаграммами отображается количество пользователей и групп с новыми назначениями ролей за последние 30 дней (слева) и список ролей, отсортированных по общему числу назначений (по убыванию).

![](media/azure-pim-resource-rbac/role-settings.png)

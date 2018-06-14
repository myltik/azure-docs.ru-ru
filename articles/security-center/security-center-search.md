---
title: Поиск в центре безопасности Azure | Документация Майкрософт
description: Узнайте, как центр безопасности Azure получает и анализирует данные безопасности с помощью поиска Log Analytics.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040439"
---
# <a name="azure-security-center-search"></a>Поиск в центре безопасности Azure
Центр безопасности Azure получает и анализирует данные безопасности с помощью [поиска Log Analytics](../log-analytics/log-analytics-log-searches.md). Служба Log Analytics поддерживает язык запросов для быстрого получения и консолидации данных. В центре безопасности поиск Log Analytics можно использовать для создания запросов и анализа собранных данных.

Поиск доступен в ценовой категории "Бесплатный" и "Стандартный" центра безопасности.  Данные, доступные в поисковых запросах, зависят от категории рабочей области.  Дополнительные сведения см. на странице [цен на центр безопасности](../security-center/security-center-pricing.md).


> [!NOTE]
> На уровне "Бесплатный" центр безопасности не сохраняет данные безопасности для рабочей области. На этом уровне вы можете отправлять различные журналы в рабочую область и выполнять поиск этих данных, но в результатах такого поиска не будет данных из центра безопасности. Центр безопасности сохраняет данные в рабочую область только на уровне "Стандартный".
>
>

## <a name="access-search"></a>Получение доступа к поиску
1. В главном меню центра безопасности выберите **Поиск**.

  ![Выбор поиска по журналам][1]

2. В центре безопасности отображаются все рабочие области в подписках Azure. Выберите рабочую область. При наличии одной рабочей области этот селектор рабочей области не отображается.

  ![Выбор рабочей области][2]

3. Откроется окно **Log Search** (Поиск по журналам). Чтобы запросить дополнительные данные в выбранной рабочей области, введите следующий пример запроса:

  SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

  В результатах содержатся все учетные записи, в которые не удалось выполнить вход (событие 4625).

  ![Результаты поиска][3]

Дополнительные сведения о выполнении запроса данных в выбранной рабочей области см. в статье [Справочник по поиску в Log Analytics](../log-analytics/log-analytics-search-reference.md).

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как получить доступ к поиску в центре безопасности. Центр безопасности использует поиск Log Analytics. Дополнительные сведения о поиске Log Analytics см. в следующих статьях:

- [Что такое Log Analytics?](../log-analytics/log-analytics-overview.md) Общие сведения о Log Analytics.
- [Основные сведения о поисках по журналам в Log Analytics](../log-analytics/log-analytics-log-search-new.md). Здесь представлены сведения об использовании поиска по журналам в Log Analytics и основные понятия, которые нужно рассмотреть перед созданием поиск по журналам.
- [Поиск данных по журналам в Log Analytics](../log-analytics/log-analytics-log-searches.md). Руководство по использованию поиска по журналам.
- [Справочник по поиску в Log Analytics](../log-analytics/log-analytics-search-reference.md). Здесь описывается язык запросов в Log Analytics.

Дополнительные сведения о центре безопасности см. в следующих статьях:

- [Введение в Центр безопасности Azure](security-center-intro.md). Здесь описываются основные возможности центра безопасности.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png

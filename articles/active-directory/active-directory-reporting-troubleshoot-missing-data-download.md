---
title: Устранение неполадок, связанных с отсутствием данных в скачанных журналах действий Azure Active Directory | Документация Майкрософт
description: В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в скачанных журналах действий.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 6878ff8175514b97fbeab70b932349ce400394dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589871"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Не удается найти данные в скачанном журнале действий Azure Active Directory


## <a name="symptoms"></a>Симптомы

В скачанном журнале действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Причина:

К журналам действий, скачиваемым на портале Azure, применяется ограничение в 120 тыс. последних записей. 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](active-directory-reporting-api-getting-started.md), чтобы извлечь до миллиона записей. Мы рекомендуем настроить расписание выполнения скрипта, который вызывает интерфейсы API отчетов для получения данных за определенный период времени (например, ежедневно или еженедельно).

## <a name="next-steps"></a>Дополнительная информация
См. сведения в статье [Часто задаваемые вопросы об отчетах Azure Active Directory](active-directory-reporting-faq.md).


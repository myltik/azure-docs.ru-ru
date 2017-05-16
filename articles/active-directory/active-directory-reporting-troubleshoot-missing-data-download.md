---

title: "Устранение неполадок, связанных с отсутствием данных в скачанных журналах действий Azure Active Directory | Документация Майкрософт"
description: "В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в скачанных журналах действий."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9109c698e4e8b43eeb7534c338adc99476012a3f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Не удается найти данные в скачанном журнале действий Azure Active Directory


## <a name="symptoms"></a>Симптомы

В скачанном журнале действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Причина:

К журналам действий, скачиваемым на портале Azure, применяется ограничение в 120 тыс. последних записей. 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](active-directory-reporting-api-getting-started.md), чтобы извлечь до миллиона записей. Мы рекомендуем настроить расписание выполнения скрипта, который вызывает интерфейсы API отчетов для получения данных за определенный период времени (например, ежедневно или еженедельно).

## <a name="next-steps"></a>Дальнейшие действия
См. сведения в статье [Часто задаваемые вопросы об отчетах Azure Active Directory](active-directory-reporting-faq.md).



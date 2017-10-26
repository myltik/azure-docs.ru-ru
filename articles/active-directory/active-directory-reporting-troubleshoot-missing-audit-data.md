---
title: "Устранение неполадок, связанных с отсутствием данных в журнале действий Azure Active Directory | Документация Майкрософт"
description: "В этой статье описаны различные отчеты, доступные в Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4e63c9b48125e3c7283be5fe88cab211509f88f4
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2017
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Не удается найти некоторые выполненные действия в журнале действий Azure Active Directory


## <a name="symptoms"></a>Симптомы

Действия, выполненные на портале Azure, не отображаются в журнале аудита в колонке `Activity logs > Audit Logs`.

 ![Отчеты](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Причина:

Действия отображаются в журнале аудита действий спустя некоторое время. Чтобы действия отобразились в журнале аудита на портале, с момента выполнения операции может пройти от 15 минут до часа.

## <a name="resolution"></a>Способы устранения:

Подождите от 15 минут до часа и проверьте, появились ли действия в журнале. Если они по-прежнему отсутствуют, отправьте запрос в службу поддержки, и мы рассмотрим вашу проблему.


## <a name="next-steps"></a>Дальнейшие действия
См. сведения в статье [Часто задаваемые вопросы об отчетах Azure Active Directory](active-directory-reporting-faq.md).


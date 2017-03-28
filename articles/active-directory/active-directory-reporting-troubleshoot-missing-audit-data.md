---
title: "Устранение неполадок, связанных с отсутствием данных в журнале действий Azure Active Directory (предварительная версия) | Документация Майкрософт"
description: "В этой статье описаны различные отчеты для устранения неполадок, доступные в предварительной версии Azure Active Directory."
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
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: ebc92bc23201dbcd01f90e0cc3d5055fb85820be
ms.lasthandoff: 03/10/2017


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



---
title: "Вопросы и ответы о защите идентификации Azure Active Directory | Документация Майкрософт"
description: "Часто задаваемые вопросы о защите идентификации Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 102cf0108aeefa255d40bd932e41f1e7b5536205
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-faq"></a>Вопросы и ответы о защите идентификации Azure Active Directory

Эта статья содержит ответы на часто задаваемые вопросы о защите идентификации Azure Active Directory (Azure AD). Дополнительные сведения см. в статье [Защита идентификации Azure Active Directory](active-directory-identityprotection.md). 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>Почему для некоторых событий риска отображается состояние "Закрыто (системные)"?

**Ответ.** Это события, обнаруженные и затем закрытые службой защиты идентификации Azure Active Directory, так как они перестали считаться событиями, представляющими риск. Эти события не учитываются в уровне риска пользователя. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Требуется ли роль глобального администратора для использования защиты идентификации на портале Azure?
**Ответ.** Нет. Для использования защиты идентификации можно иметь роль читателя безопасности, администратора безопасности или глобального администратора.

---

## <a name="how-do-i-get-identity-protection"></a>Как начать использовать защиту идентификации?

**Ответ.** Ответ на этот вопрос есть в разделе [Приступая к работе с Azure Active Directory Premium](active-directory-get-started-premium.md).

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Как сортировать пользователей в разделе "Пользователи, находящиеся в группе риска"?

**Ответ.** Скачайте отчет по пользователям, находящимся в группе риска, нажав кнопку **Загрузить** в верхней части страницы **Пользователи, находящиеся в группе риска**. Затем можно отсортировать скачанные данные на основе доступных полей, таких как "Последнее обновление (в формате UTC)".

---

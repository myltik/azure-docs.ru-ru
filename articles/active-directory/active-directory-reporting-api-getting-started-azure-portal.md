---
title: "Начало работы с API отчетов Azure AD | Документация Майкрософт"
description: "Как начать работу с API отчетов Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9c858b8f2d5a4a348bc0b4443ddbe0000a5b62f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Приступая к работе с API отчетов Azure Active Directory

Azure Active Directory предоставляет разнообразные отчеты. Данные этих отчетов могут быть очень полезными для приложений, например систем SIEM, а также инструментов аудита и бизнес-аналитики. Интерфейсы API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

В этой статье содержатся сведения, необходимые для начала работы с интерфейсами API отчетов Azure AD.
В следующем разделе представлены дополнительные сведения об использовании интерфейсов API аудита и входа. 

Ответы на вопросы см. в разделе [Часто задаваемые вопросы](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-faq). При возникновении проблем [создайте запрос в службу поддержки](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>Карта обучения
1. **Подготовка.** Перед тестированием примеров API нужно выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Изучение.** Ознакомьтесь с интерфейсами API отчетов:
   
   * [Использование примеров для API аудита](active-directory-reporting-api-audit-samples.md) 
   * [Использование примеров для API отчетов о действиях при входе](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Настройка.** Создайте свое решение: 
   
   * [Справочник по использованию API аудита](active-directory-reporting-api-audit-reference.md) 
   * [Справочник по использованию API отчетов о действиях при входе](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите ознакомиться со всеми доступными конечными точками API Graph Azure AD, воспользуйтесь этой ссылкой: [https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).


---
title: "Начало работы с API отчетов Azure AD | Документация Майкрософт"
description: "Как начать работу с API отчетов Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 9944cbd2b1b7c4acb18d37da1394c0bbc170f77d
ms.contentlocale: ru-ru
ms.lasthandoff: 08/19/2017

---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Приступая к работе с API отчетов Azure Active Directory

Azure Active Directory предоставляет разнообразные отчеты. Данные этих отчетов могут быть очень полезными для приложений, например систем SIEM, а также инструментов аудита и бизнес-аналитики. Интерфейсы API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

В этой статье содержатся сведения, необходимые для начала работы с интерфейсами API отчетов Azure AD.
В следующем разделе представлены дополнительные сведения об использовании интерфейсов API аудита и входа. 

Ответы на вопросы см. в разделе [Часто задаваемые вопросы](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq). При возникновении проблем [создайте запрос в службу поддержки](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)

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



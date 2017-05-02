---
title: "Непредвиденный запрос на согласие при входе в приложение | Документы Майкрософт"
description: "Способы устранения неполадок, когда пользователь видит неожиданный запрос на согласие для приложения, которое вы интегрировали с Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: f32de2a8868d853e7720cffa23df6b3c59ab4a49
ms.lasthandoff: 04/17/2017


---

# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Непредвиденный запрос на согласие при входе в приложение

Для правильной работы многим приложениям, интегрирующимся с Azure Active Directory, нужны разрешения на доступ к разным ресурсам. Если эти ресурсы также интегрированы с Azure Active Directory, доступ к ним запрашивается с помощью инфраструктуры согласия Azure AD. 

Это приводит к отображению запроса на согласие при первом запуске приложения, что часто является единовременной операцией. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Сценарии, в которых пользователи видят запрос на согласие

Дополнительные запросы можно ожидать в разных сценариях:

* Изменился набор требуемых приложению разрешений.

* Пользователь, который изначально дал согласие для приложения, не являлся администратором, а теперь другой пользователь (без прав администратора) впервые открывает приложение.

* Пользователь, который изначально дал согласие для приложения, являлся администратором, однако он сделал это не от имени всей организации.

* Приложение использует [добавочное и динамическое согласие](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) для запроса дополнительных разрешений после изначального предоставления согласия. Такой подход часто применяется, когда расширенным функциям приложения нужны дополнительные разрешения сверх базового набора.

* Согласие было отозвано после первоначального предоставления.

* Разработчик настроил приложение для обязательного запроса на согласие при каждом использовании (обратите внимание, что такой подход не является рекомендуемым).

## <a name="next-steps"></a>Дальнейшие действия

-   [Приложения, разрешения и согласие в Azure Active Directory (конечная точка версии 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Области, разрешения и согласие в Azure Active Directory (конечная точка версии 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)




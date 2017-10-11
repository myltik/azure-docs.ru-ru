---
title: "Интерактивная настройка Azure AD версии 2 для приложений JS SPA. Введение | Документация Майкрософт"
description: "В этой статье описано, как приложения JavaScript SPA могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 3d195d0d67f8f82c9450ffd93767917698addee3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вызов API Microsoft Graph из одностраничного приложения JavaScript

В этом руководстве показано, как одностраничное приложение JavaScript может выполнять вход с помощью личных, рабочих или учебных учетных записей, получать маркер доступа и вызывать API Microsoft Graph или другие API, требующие маркеры доступа, из конечной точки Azure Active Directory версии 2.

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Как работает пример приложения, созданный в этом руководстве](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный в этом руководстве, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии после выполнения входа в систему маркер доступа запрашивается и добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|Описание|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|

> [!NOTE]
> В качестве целевого объекта в библиотеке *msal.js* задана *конечная точка Azure Active Directory версии 2*, что позволяет выполнять вход и запрашивать маркеры, используя личные, рабочие и учебные учетные записи. В отношении *конечной точки Azure Active Directory версии 2* применяется [ряд ограничений](..\active-directory-v2-limitations.md). Если вы заинтересованы только в учебной и рабочей учетных записях, используйте библиотеку *adal.js* и *конечную точку версии 1*. Чтобы понять различия между конечными точками версий 1 и 2, ознакомьтесь со [сравнением версий 1 и 2](..\active-directory-v2-compare.md).

<!--end-collapse-->

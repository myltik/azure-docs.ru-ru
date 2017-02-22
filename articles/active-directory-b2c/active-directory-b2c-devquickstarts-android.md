---
title: "Azure Active Directory B2C: вызов веб-API из приложения Android | Документация Майкрософт"
description: "В этой статье показано, как создать приложение Android &quot;Список дел&quot;, вызывающее веб-API Node.js с помощью токенов носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: вызов веб-API из приложения Android
> [!WARNING]
> Мы опубликовали обновленный пример кода для Android, который можно найти [здесь](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi).  В этом примере используется библиотека стороннего производителя, которая была протестирована в основных сценариях на совместимость со службой Azure AD B2C.  Эта библиотека использует встроенные веб-представления вместо системного браузера.  Компания Google [объявила](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html), что с 20 апреля 2017 г. будет прекращена поддержка входа в учетную запись Google с помощью встроенных веб-представления. Следовательно те, кто хочет и далее пользоваться учетными записями Google, должны будут обновить библиотеки.  

>Корпорация Майкрософт не предоставляет исправления для библиотек сторонних производителей и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>




<!--HONumber=Feb17_HO1-->



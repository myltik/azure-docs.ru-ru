---
title: "Приступая к работе с Azure AD версии 2 для Android. Общие сведения | Документация Майкрософт"
description: "Получение маркера доступа для приложения Android и вызов API Microsoft Graph или API, которые требуют маркер доступа, из конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Вызов API Microsoft Graph из приложения Android

В этом руководстве показано, как приложение Android может получить маркер доступа и вызвать API Microsoft Graph или другие API, требующие маркеры доступа, из конечной точки Azure Active Directory версии 2.

В конце этого руководства ваше приложение сможет вызывать защищенный API с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory.  

### <a name="how-this-sample-works"></a>Как работает этот пример
![Как работает этот пример](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

Пример, созданный в этом руководстве, основан на сценарии, в котором приложение Android используется для запроса веб-API, который принимает маркеры из конечной точки Azure Active Directory версии 2. В этом случае — API Microsoft Graph. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

### <a name="pre-requisites"></a>Предварительные требования
* Это руководство по установке ориентировано на Android Studio, но его также можно использовать для любой другой среды разработки приложений Android. 
* Необходим пакет SDK для Android версии 21 или более поздней (советуем использовать пакет SDK версии 25).
* Для этого выпуска библиотеки проверки подлинности Майкрософт (MSAL) для Android требуется Google Chrome или браузер, поддерживающий настраиваемые вкладки.

> Примечание. Google Chrome не входит в эмулятор Android для Visual Studio. Советуем проверить этот код на эмуляторе с API версии 25 или образе с API версии 21 или более поздней с установленным Google Chrome.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Как обрабатывается получение маркера для доступа к защищенным веб-API

После того как пользователь пройдет проверку подлинности, пример приложения получит маркер, который может использоваться для запроса API Microsoft Graph или веб-API, защищенного Microsoft Azure Active Directory версии 2.

API, такие как Microsoft Graph, требуют маркер доступа для доступа к определенным ресурсам. Например, для чтения профиля пользователя, доступа к календарю пользователя или отправки почты. Приложение может запросить маркер доступа с помощью MSAL, чтобы получить доступ к этим ресурсам, указав определенные области API. Затем этот маркер доступа добавляется в заголовок авторизации HTTP для каждого вызова к защищенному ресурсу. 

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.

### <a name="libraries"></a>Библиотеки

В этом руководстве используются следующие библиотеки:

|Библиотека|Описание|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Библиотека проверки подлинности Майкрософт (MSAL)|

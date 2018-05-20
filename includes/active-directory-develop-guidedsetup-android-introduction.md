---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: aa21168f1e19ccddc190379b30e1b612d96b56d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="sign-in-users-and-all-the-microsoft-graph-api-from-an-android-app"></a>Вход пользователей и вызов всех API Microsoft Graph из приложения Android

В этом руководстве показано, как собственное приложение Android может получить маркер доступа и вызвать API Microsoft Graph или другие API, которым требуются маркеры доступа, из конечной точки Azure Active Directory версии 2.

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory. Затем приложение сможет вызвать программный интерфейс, защищенный конечной точкой Azure Active Directory версии 2.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве
![Как работает этот пример](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

Пример приложения, созданный в этом руководстве, основан на сценарии, в котором приложение Android используется для запроса веб-API, принимающего маркеры из конечной точки Azure Active Directory версии 2 (в нашем случае — API Microsoft Graph). В этом сценарии приложение добавляет полученный маркер в HTTP-запросы через заголовок авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

## <a name="prerequisites"></a>предварительным требованиям
* Это руководство по настройке предназначено для Android Studio, но его также можно использовать для любой другой среды разработки приложений Android. 
* Необходим пакет SDK для Android версии 21 или более поздней (советуем использовать пакет SDK версии 25).
* Для этого выпуска MSAL для Android требуется Google Chrome или браузер, поддерживающий настраиваемые вкладки.

> [!NOTE]
> Google Chrome не входит в эмулятор Android для Visual Studio. Советуем проверить этот код на эмуляторе с API версии 25 либо в образе с API версии 21 или более поздней с установленным браузером Google Chrome.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Обработка получения маркера для доступа к защищенным веб-интерфейсам API

После того как пользователь пройдет проверку подлинности, пример приложения получит маркер доступа, который может использоваться для запроса API Microsoft Graph или веб-API, защищенного с помощью Azure Active Directory версии 2.

Программным интерфейсам, таким как Microsoft Graph, для доступа к определенным ресурсам требуется маркер доступа. Например, маркер доступа необходим для чтения профиля пользователя, доступа к календарю пользователя и отправки электронной почты. Приложение может запросить маркер доступа с помощью MSAL, чтобы получить доступ к этим ресурсам, указав определенные области API. Затем этот маркер доступа добавляется в заголовок авторизации HTTP для каждого вызова к защищенному ресурсу. 

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.

## <a name="libraries"></a>Библиотеки

В этом руководстве используются следующие библиотеки:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Библиотека проверки подлинности Майкрософт (MSAL)|

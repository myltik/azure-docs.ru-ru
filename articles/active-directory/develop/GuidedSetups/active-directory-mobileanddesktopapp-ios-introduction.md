---
title: "Приступая к работе с Azure AD версии 2 для iOS. Введение | Документация Майкрософт"
description: "В этой статье описано, как приложения iOS (Swift) могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2."
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 5fcd78eaa0c5b09b70aa973466a34556fff56071
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Вызов API Microsoft Graph из приложения iOS

В этом руководстве показано, как собственное приложение iOS (Swift) может получить маркер доступа и вызвать API Microsoft Graph или другие API, для которых требуются маркеры доступа, из конечной точки Azure Active Directory версии 2.

В конце этого руководства ваше приложение сможет вызывать защищенный API с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory.

> ### <a name="pre-requisites"></a>Предварительные требования
> - Для этого руководства требуется XCode 8.x. Скачать XCode можно [здесь](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL-адрес для скачивания XCode").
> - [Carthage](https://github.com/Carthage/Carthage) для управления пакетами

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Принцип работы с руководством](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

Пример приложения, созданный в этом руководстве, позволяет приложению iOS выполнять запрос к API Microsoft Graph или веб-API, который принимает маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Обработка получения маркера для доступа к защищенным веб-интерфейсам API

После того как пользователь пройдет проверку подлинности, приложение получит маркер, который может использоваться для запроса к API Microsoft Graph или к веб-API, защищенному Microsoft Azure Active Directory версии 2.

API, такие как Microsoft Graph, требуют маркер доступа для доступа к определенным ресурсам. Например, для чтения профиля пользователя, доступа к календарю пользователя или отправки почты. Приложение может запросить маркер доступа с помощью MSAL, указав области API. Затем этот маркер доступа добавляется в заголовок авторизации HTTP для каждого вызова к защищенному ресурсу.

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.


### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|Описание|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Предварительная версия библиотеки проверки подлинности Microsoft для iOS|


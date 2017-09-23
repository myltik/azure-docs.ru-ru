---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Общие сведения | Документация Майкрософт"
description: "Здесь описывается, как классические приложения для Windows .NET (XAML) могут вызвать API, требующий маркеры доступа от конечной точки Azure Active Directory версии 2."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.contentlocale: ru-ru

---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Вызов API Microsoft Graph из классического приложения для Windows

В этом руководстве описывается, как классическое приложение для Windows .NET (XAML) может получить маркер доступа и вызвать API Microsoft Graph или другие API, требующие маркеры доступа от конечной точки Azure Active Directory версии 2.

В конце этого руководства ваше приложение сможет вызывать защищенный API с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory.  

> Для работы с этим руководством требуется Visual Studio 2015 с обновлением 3 или Visual Studio 2017.  У вас его нет? [Скачайте Visual Studio 2017 бесплатно.](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Принцип работы с руководством

![Принцип работы с руководством](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

Пример приложения, созданный в этом руководстве, позволяет классическому приложению для Windows выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Обработка получения маркера для доступа к защищенным веб-интерфейсам API

После того как пользователь пройдет проверку подлинности, пример приложения получит маркер, который может использоваться для запроса API Microsoft Graph или веб-API, защищенного Microsoft Azure Active Directory версии 2.

API, такие как Microsoft Graph, требуют маркер доступа для доступа к определенным ресурсам. Например, для чтения профиля пользователя, доступа к календарю пользователя или отправки почты. Приложение может запросить маркер доступа с помощью MSAL, чтобы получить доступ к этим ресурсам, указав определенные области API. Затем этот маркер доступа добавляется в заголовок авторизации HTTP для каждого вызова к защищенному ресурсу. 

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.


### <a name="nuget-packages"></a>Пакеты NuGet

В этом руководстве используются следующие пакеты NuGet:

|Библиотека|Описание|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Библиотека проверки подлинности Майкрософт (MSAL)|



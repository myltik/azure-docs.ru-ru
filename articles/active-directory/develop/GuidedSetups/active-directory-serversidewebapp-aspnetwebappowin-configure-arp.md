---
title: "Приступая к работе с Azure AD версии 2 для веб-сервера ASP.NET. Настройка | Документация Майкрософт"
description: "Реализация входа Майкрософт в решении ASP.NET с традиционным браузерным приложением с использованием стандарта OpenID Connect"
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
ms.openlocfilehash: 6c6a44cbbd79cf6422515a6d70d48ac12cba4f9f
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Настройка веб-приложения ASP.NET с помощью сведений о регистрации приложения

На этом шаге вам предстоит настроить проект для использования SSL, а затем использовать URL-адрес SSL для настройки сведений о регистрации приложения. После этого вы добавите сведения о регистрации приложения в свое решение, используя файл *web.config*.

1.  В обозревателе решений выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу F4).
2.  Задайте для параметра `SSL Enabled` значение `True`:<br/>
![Свойства проекта](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
3.  Скопируйте значение указанного выше `SSL URL` и вставьте его в поле `Redirect URL` в верхней части этой страницы, а затем щелкните *Обновление*:
4.  Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```


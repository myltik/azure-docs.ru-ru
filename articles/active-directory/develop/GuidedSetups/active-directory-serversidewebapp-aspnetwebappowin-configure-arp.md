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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b10c8bd6878f6891b2420ae7fb4b65b19144762c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Настройка веб-приложения ASP.NET с помощью сведений о регистрации приложения

На этом шаге вам предстоит настроить проект для использования SSL, а затем использовать URL-адрес SSL для настройки сведений о регистрации приложения. После этого вы добавите сведения о регистрации приложения в свое решение, используя файл *web.config*.

1.    В обозревателе решений выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу F4).
2.    Задайте для параметра `SSL Enabled` значение `True`:<br/>
![Свойства проекта](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
3.    Скопируйте значение указанного выше `SSL URL` и вставьте его в поле `Redirect URL` в верхней части этой страницы, а затем щелкните *Обновление*:
4.    Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```


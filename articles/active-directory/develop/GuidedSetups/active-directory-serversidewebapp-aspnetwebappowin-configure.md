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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: ru-ru


---

## <a name="create-an-application-express"></a>Создание приложения (экспресс)
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.    Введите имя для приложения и адрес электронной почты.
3.    Выберите параметр Guided Setup (Пошаговая настройка).
4.    Следуйте дальнейшим инструкциям, чтобы добавить URL-адрес перенаправления в приложение.

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Добавление сведений о регистрации приложения в решение (дополнительно)
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя для приложения и адрес электронной почты. 
3.    Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4.    Щелкните `Add Platforms`, а затем выберите `Web`.
5.    Вернитесь в Visual Studio и в обозревателе решений выберите проект и просмотрите окно свойств (если окно свойств не отображается, нажмите клавишу F4).
6.    Для параметра "SSL включен" измените значение на `True`:<br/><br/>![Свойства проекта](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    Скопируйте URL-адрес SSL и добавьте его в список URL-адресов перенаправления в списке на портале регистрации.
8.    Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. Замените `ClientId` зарегистрированным идентификатором приложения.
10. Замените `redirectUri` URL-адресом SSL проекта. 
<!-- End Docs -->

> Примечание
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>Разрешение входа в приложение только пользователям одной организации
> По умолчанию в приложение можно выполнить вход с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory. Если вы хотите, чтобы вход в приложение был настроен только для пользователей одной организации, замените параметр `Tenant` в файле `web.config` с `Common` на имя клиента организации, например `contoso.onmicrosoft.com`. После этого измените аргумент *ValidateIssuer* в классе запуска OWIN, задав ему значение `true`.
Чтобы разрешить вход для пользователей из списка определенных организаций, задайте параметру `ValidateIssuer` значение `true` и используйте параметр `ValidIssuers`, чтобы указать список организаций.
Другой вариант — реализовать пользовательский метод для проверки издателей с помощью `IssuerValidator parameter`. Дополнительные сведения о `TokenValidationParameters` см. в [этой статье MSDN](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx).



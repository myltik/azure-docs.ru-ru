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
ms.openlocfilehash: c1971e1eb3abc653ad8bdc6af772c699f8549019
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application"></a>Регистрация приложения
Зарегистрировать приложение и добавить сведения о его регистрации в решение можно двумя способами:

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим

Чтобы быстро зарегистрировать приложение, сделайте следующее:
1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр Guided Setup (Пошаговая настройка).
4.  Следуйте дальнейшим инструкциям, чтобы добавить URL-адрес перенаправления в приложение.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя для приложения и адрес электронной почты. 
3.  Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4.  Щелкните `Add Platform`, а затем выберите `Web`.
5.  Вернитесь в Visual Studio и в обозревателе решений выберите проект и просмотрите окно свойств (если окно свойств не отображается, нажмите клавишу F4).
6.  Для параметра "SSL включен" измените значение на `True`.
7.  Скопируйте URL-адрес SSL и добавьте его в список URL-адресов перенаправления в списке на портале регистрации:<br/><br/>![Свойства проекта](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Добавьте следующий код в файл `web.config`, расположенный в корневой папке в разделе `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
    ```

9. Замените `ClientId` зарегистрированным идентификатором приложения.
10. Замените `redirectUri` URL-адресом SSL проекта. 


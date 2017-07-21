---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Настройка | Документация Майкрософт"
description: "Здесь описывается, как классическое приложение для Windows .NET (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2. | Microsoft Azure | Microsoft Azure"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1dfaa7ade664e43dcb9aa788b0197ca17e6ec4cc
ms.contentlocale: ru-ru


---

## <a name="create-an-application-express"></a>Создание приложения (экспресс)
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр Guided Setup (Пошаговая настройка).
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Добавление сведений о регистрации приложения в решение (дополнительно)
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя для приложения и адрес электронной почты. 
3. Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4. Щелкните `Add Platform`, а затем — `Native Application` и нажмите кнопку "Сохранить".
5. Скопируйте GUID в идентификатор приложения, вернитесь в Visual Studio, откройте `App.xaml.cs` и замените `your_client_id_here` зарегистрированным идентификатором приложения:

```csharp
private static string ClientId = "your_application_id_here";
```


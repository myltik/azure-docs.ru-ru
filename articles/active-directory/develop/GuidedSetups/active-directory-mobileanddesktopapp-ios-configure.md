---
title: "Приступая к работе с Azure AD версии 2 для iOS. Настройка | Документация Майкрософт"
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
ms.openlocfilehash: 0ebca65585fc87bd4a85ba092cd423fce9540f58
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
## <a name="create-an-application-express"></a>Создание приложения (экспресс)
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр Guided Setup (Пошаговая настройка).
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Добавление сведений о регистрации приложения в решение (дополнительно)

1.  Перейдите на [портал регистрации приложений](https://apps.dev.microsoft.com/portal/register-app)
2.  Введите имя для приложения и адрес электронной почты.
3.  Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4.  Щелкните `Add Platform`, затем выберите `Native Application` и щелкните `Save`.
5.  Вернитесь в Xcode. В `ViewController.swift` замените строку, начинающуюся с '`let kClientID`', на зарегистрированный идентификатор приложения:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Щелкните <code>Info.plist</code>, удерживая клавишу CTRL, чтобы открыть контекстное меню, затем щелкните <code>Open As</code> > <code>Source Code</code>
.</li>
<li>
Добавьте следующий элемент в корневой узел <code>dict</code>:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Замените <i><code>[Your_Application_Id_Here]</code></i> зарегистрированным идентификатором приложения.
</li>
</ol>

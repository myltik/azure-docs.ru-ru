---
title: "Интерактивная настройка Azure AD версии 2 для приложений JS SPA | Документация Майкрософт"
description: "В этой статье описано, как приложения JavaScript SPA могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2."
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
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: adff529bfdc40006666cc643d49a302d7f1d09ad
ms.contentlocale: ru-ru

---
## <a name="register-your-application"></a>Регистрация приложения

Есть несколько способов создавать приложения. Выберите один из них:

### <a name="option-1-register-your-application-express-mode"></a>Вариант 1. Регистрация приложения (экспресс-режим).
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:

1.  Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр *Guided Setup* (Пошаговая настройка).
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-register-your-application-advanced-mode"></a>Вариант 2. Регистрация приложения (расширенный режим).

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите имя для приложения и адрес электронной почты. 
3. Убедитесь, что параметр *Guided Setup* (Пошаговая настройка) не выбран.
4.  Щелкните `Add Platform`, а затем выберите `Web`.
5. Добавьте URL-адрес перенаправления (`Redirect URL`), соответствующий URL-адресу приложения, в зависимости от веб-сервера. Инструкции по определению и получению URL-адреса перенаправления в Visual Studio и Python см. в разделах ниже.
6. Нажмите кнопку *Сохранить*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Инструкции Visual Studio для получения URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, сделайте следующее:
> 1.    В *обозревателе решений* выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу `F4`).
> 2.    Скопируйте значение `URL` в буфер обмена:<br/> ![Свойства проекта](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Вернитесь на *портал регистрации приложений* и вставьте значение `Redirect URL`, а затем нажмите кнопку "Сохранить".

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Настройка URL-адреса перенаправления для Python
> Для Python можно задать порт веб-сервера через командную строку. В этой интерактивной настройке в качестве примера используется порт 8080, но можно использовать любой другой доступный порт. Как бы то ни было, выполните инструкции ниже, чтобы настроить URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> - Вернитесь на *портал регистрации приложения* и укажите для `http://localhost:8080/` значение `Redirect URL`. Или укажите `http://localhost:[port]/`, если вы используете другой номер TCP-порта (где *[port]* — это номер пользовательского TCP-порта). Затем нажмите кнопку "Сохранить".


#### <a name="configure-your-javascript-spa"></a>Настройка одностраничного приложения JavaScript

1.  Создайте файл с именем `msalconfig.js`, в котором будут содержаться сведения о регистрации приложения. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `JavaScript File`. Назовите класс `msalconfig.js`.
2.  Добавьте в файл `msalconfig.js` следующий код:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Замените <code>Enter_the_Application_Id_here</code> зарегистрированным идентификатором приложения.
</li>
</ol>


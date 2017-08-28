---
title: "Интерактивная настройка Azure AD версии 2 для приложений JS SPA — настройка (ARP) | Документация Майкрософт"
description: "В этой статье описано, как приложения JavaScript SPA могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2 (ARP)."
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
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 735559464db4c237ff7498b7350f2385090fa4e4
ms.contentlocale: ru-ru
ms.lasthandoff: 08/16/2017

---
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге необходимо настроить URL-адрес перенаправления сведений о регистрации приложения, а затем добавить идентификатор приложения в одностраничное приложение JavaScript.

### <a name="configure-redirect-url"></a>Настройка URL-адреса перенаправления

Настройте указанное выше поле `Redirect URL`, используя URL-адрес страницы index.html на основе веб-сервера, а затем нажмите кнопку *Обновить*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Инструкции Visual Studio для получения URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, следуйте инструкциям ниже.
> 1.    В *обозревателе решений* выберите проект и просмотрите окно `Properties` (если окно свойств не отображается, нажмите клавишу `F4`).
> 2.    Скопируйте значение `URL` в буфер обмена:<br/> ![Свойства проекта](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Вставьте это значение в поле `Redirect URL` в верхней части этой страницы, а затем нажмите кнопку `Update`.

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Настройка URL-адреса перенаправления для Python
> Для Python можно задать порт веб-сервера через командную строку. В этой интерактивной настройке в качестве примера используется порт 8080, но можно использовать любой другой доступный порт. Как бы то ни было, выполните инструкции ниже, чтобы настроить URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> В верхней части этой страницы выберите `http://localhost:8080/` в качестве `Redirect URL` или воспользуйтесь `http://localhost:[port]/`, если используете настраиваемый TCP-порт (где *[port]* — это номер настраиваемого TCP-порта), а затем нажмите кнопку "Обновить".

### <a name="configure-your-javascript-spa-application"></a>Настройка одностраничного приложения JavaScript

1.  Создайте файл с именем `msalconfig.js`, в котором будут содержаться сведения о регистрации приложения. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `JavaScript File`. Назовите класс `msalconfig.js`.
2.  Добавьте в файл `msalconfig.js` следующий код:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 


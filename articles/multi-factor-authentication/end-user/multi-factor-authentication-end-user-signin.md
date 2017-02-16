---
title: "Вход с помощью Azure MFA с использованием двухфакторной проверки подлинности | Документация Майкрософт"
description: "На этой странице предоставлены рекомендации о том, где можно воспользоваться разными методами входа с помощью Azure MFA."
keywords: "проверка подлинности пользователя, вход в систему, вход с помощью мобильного телефона, вход с помощью рабочего телефона"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 63951136d4f7bc2eb81c24ddfd58e696fc31cf37


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Варианты входа при помощи Azure Multi-Factor Authentication
> [!NOTE]
> В документации, указанной на этой странице, описана типичная процедура входа в систему.  В случае возникновения проблем со входом в систему ознакомьтесь с разделом [Проблемы с Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md).
>
>

## <a name="what-will-your-sign-in-experience-be"></a>Каким будет ваш вариант входа
Процесс входа зависит от избранного способа и использования многофакторной проверки подлинности.  В этом разделе предоставляются сведения о том, что следует ожидать при входе.  Ниже выберите вариант, который соответствует вашим действиям.

| Ваш способ входа | Описание |
|:--- |:--- |
| [Вход с помощью мобильного или рабочего телефона](#signing-in-with-mobile-or-office-phone) |Таким будет вход с мобильного или рабочего телефона. |
| [Вход через приложение Microsoft Authenticator с использованием уведомления](#signing-in-with-the-microsoft-authenticator-app-using-notification) |По этой ссылке приведены сведения о проверке подлинности через приложение Microsoft Authenticator с использованием уведомлений. |
| [Вход через приложение Microsoft Authenticator с использованием кода проверки](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |По этой ссылке приведены сведения о проверке подлинности через приложение Microsoft Authenticator с использованием кода проверки. |
| [Вход с помощью альтернативного метода](#signing-in-with-an-alternate-method) |Такая информация отобразится, если вы захотите воспользоваться альтернативным методом. |

## <a name="signing-in-with-mobile-or-office-phone"></a>Вход с помощью мобильного или рабочего телефона
В этой статье описывается процедура многофакторной проверки подлинности с помощью мобильного или рабочего телефона.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Вход с помощью вызова на рабочий или мобильный телефон
* Войдите в приложение или службу, например Office 365, используя имя пользователя и пароль.  
* Ожидайте вызов на свой телефон от корпорации Майкрософт.  
* Ответьте на вызов и нажмите клавишу #.  
* После этого вы войдете в систему.  

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Вход через приложение Microsoft Authenticator с использованием уведомления
Ниже описана процедура многофакторной проверки подлинности для входа через приложение Microsoft Authenticator с помощью отправленных уведомлений.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Вход с использованием уведомления, отправленного в приложение Microsoft Authenticator
* Войдите в приложение или службу, например Office 365, используя имя пользователя и пароль.
* Ожидайте уведомления от корпорации Майкрософт.

![Майкрософт отправляет уведомление](./media/multi-factor-authentication-end-user-signin/notify.png)

* Ответьте на него и нажмите кнопку подтверждения.  Если ваша организация требует ввода ПИН-кода, то здесь будет предложено ввести его.

![Проверка.](./media/multi-factor-authentication-end-user-signin/phone2.png)

![Настройка](./media/multi-factor-authentication-end-user-first-time/scan3.png)

* После этого вы войдете в систему.

## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Вход через приложение Microsoft Authenticator с использованием кода проверки
Ниже описана процедура использования многофакторной проверки подлинности для входа через приложение Microsoft Authenticator с помощью кода подтверждения.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Вход через приложение Microsoft Authenticator с использованием кода проверки
* Войдите в приложение или службу, например Office 365, используя имя пользователя и пароль.
* Вам будет предложено ввести код подтверждения.

![Введите код проверки](./media/multi-factor-authentication-end-user-signin/verify3.png)

* Откройте приложение Microsoft Authenticator на телефоне и введите код в поле для входа.

![Получить код](./media/multi-factor-authentication-end-user-signin/phone3.png)

* После этого вы войдете в систему.

## <a name="signing-in-with-an-alternate-method"></a>Вход с помощью альтернативного метода
Ниже приводится процедура входа с помощью альтернативного метода, когда основной метод недоступен.

### <a name="to-sign-in-with-an-alternate-method"></a>Вход с помощью альтернативного метода
* Войдите в приложение или службу, например Office 365, используя имя пользователя и пароль.
* Выберите использование альтернативного способа проверки.  У вас появится возможность выбрать другие параметры. Их количество зависит от того, сколько их задано.

![Использовать другой метод](./media/multi-factor-authentication-end-user-signin/alt.png)

* Выберите альтернативный метод и войдите.



<!--HONumber=Dec16_HO4-->



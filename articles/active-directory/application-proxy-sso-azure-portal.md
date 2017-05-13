---
title: "Выполнение единого входа в приложения с помощью прокси приложения Azure AD | Документация Майкрософт"
description: "Включите единый вход для опубликованных локальных приложений с помощью прокси приложения Azure AD на портале Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b0454ad51a2d0edecb6f7e7748ec3b74eeefa1da
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Реализация единого входа с помощью прокси приложения Azure AD (предварительная версия)

Прокси приложения Azure Active Directory помогает повысить производительность путем публикации локальных приложений, чтобы предоставить удаленным сотрудникам безопасный доступ к ним. На портале Azure также можно настроить единый вход (SSO) в эти приложения. Теперь пользователям нужно только пройти аутентификацию в Azure AD, чтобы получить доступ к корпоративным приложениям без необходимости повторного входа.

В этой статье мы используем пример приложения с паролем, чтобы показать, как сохранение паролей упрощает единый вход. 

Вы должны были опубликовать и протестировать свое приложение с помощью прокси приложения. В противном случае выполните действия, описанные в статье [Publish applications using Azure AD Application Proxy - Public Preview](application-proxy-publish-azure-portal.md) (Публикация приложений с помощью прокси приложения Azure AD), а затем вернитесь к этой статье. 

Если вы не знаете о прокси приложения, ознакомьтесь с подробными сведениями о том, [как обеспечить безопасный удаленный доступ к локальным приложениям](active-directory-application-proxy-get-started.md).

## <a name="set-up-password-vaulting-for-your-application"></a>Настройка хранения пароля для приложения

1. Войдите на [портал Azure](https://portal.azure.com) с использованием учетной записи администратора.
2. Выберите **Azure Active Directory** > **Корпоративные приложения** > **Все приложения**.
3. В списке выберите приложение, для которого необходимо настроить единый вход. Если у вас есть много приложений, можно отфильтровать соответствующие приложения, используя поле поиска.  
4. В разделе "Управление" щелкните **Единый вход**.

   ![Выбор пункта "Единый вход"](./media/application-proxy-sso-azure-portal/select-sso.png)

5. В качестве режима единого входа выберите **Вход по паролю**.
6. В поле "URL-адрес входа" введите URL-адрес страницы, на которой пользователи вводят имена пользователей и пароли для входа в приложение. Это должен быть внешний URL-адрес, созданный при публикации приложения через прокси приложения. 

   ![Выбор входа по паролю и ввод URL-адрес](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Щелкните **Сохранить**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Тестирование приложения

Перейдите на сайт [MyApps](https://myapps.microsoft.com) и выберите настроенное приложение. Выполните вход, используя учетные данные для этого приложения (или учетные данные для тестовой учетной записи, настроенной для доступа). После успешного входа вы сможете выйти из приложения, а затем вернуться в него, не вводя учетные данные снова. 

## <a name="next-steps"></a>Дальнейшие действия

Прочитайте о других способах реализации [единого входа с помощью прокси приложения](active-directory-application-proxy-sso-using-kcd.md).


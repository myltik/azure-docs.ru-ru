---
title: "Настройка приложений SaaS для службы совместной работы B2B в Azure Active Directory | Документация Майкрософт"
description: "Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>Настройка приложений SaaS для службы совместной работы B2B

Служба совместной работы Azure Active Directory (Azure AD) B2B работает с большинством приложений, которые интегрируются с Azure AD. В этом разделе представлены пошаговые инструкции по настройке некоторых популярных приложений SAS для использования со службой Azure AD B2B.
Прежде чем рассматривать инструкции для конкретного приложения, ознакомьтесь с некоторыми общими правилами:

* Помните, что для большинства приложений подготовку пользователей необходимо выполнять вручную (то есть пользователей также следует вручную создать в приложении).

* Для приложений, поддерживающих автоматическую подготовку (например Dropbox), будут созданы отдельные приглашения от приложений. Пользователь должен принять каждое приглашение.

* В атрибутах пользователя всегда выбирайте user.mail в качестве идентификатора пользователя (во избежание проблем с искажением UPD в гостевых пользователях).


##<a name="dropbox-for-business"></a>Dropbox для бизнеса

Чтобы пользователи могли входить с помощью учетной записи организации, Dropbox для бизнеса необходимо вручную настроить на использование Azure AD в качестве поставщика удостоверений SAML. Dropbox для бизнеса не может запрашивать или другим способом позволять пользователям выполнять вход с помощью Azure AD, если эта функция не настроена.

1. Добавьте приложение Dropbox для бизнеса в Azure AD, как показано на снимке экрана.

  ![добавление Dropbox в Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![добавление Dropbox в Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. Настройте приложение.

  ![настройка единого входа для приложения](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Выберите конфигурацию единого входа и измените идентификатор пользователя на user.mail (его имя участника-пользователя по умолчанию).

4. Скачайте сертификат, который будет использоваться при настройке Dropbox.

  ![скачивание сертификата](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Воспользуйтесь параметром настройки Dropbox (этот процесс подробно представлен на снимках экрана ниже).

6. Получите URL-адрес единого входа SAML, который будет использоваться при настройке.

7. Получите URL-адрес входа на странице настройки Dropbox.

  ![вход в Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![меню Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![диалоговое окно аутентификации Dropbox свернуто](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![диалоговое окно аутентификации Dropbox развернуто](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. Передайте сертификат и вставьте здесь URL-адрес единого входа SAML.

  ![Вставка URL-адреса единого входа SAML](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Настройте автоматическую подготовку пользователей на портале Azure.

  ![настройка автоматической подготовки пользователей](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. После подготовки в приложении Dropbox гостевой пользователь или пользователь-участник получит отдельное приглашение от Dropbox. Чтобы использовать единый вход в Dropbox, приглашенные пользователи должны принять это приглашение, щелкнув ссылку.

## <a name="box"></a>Box
В этом разделе показано, как разрешить пользователям выполнять аутентификацию гостевого пользователя в Box со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. Во время этой процедуры потребуется отправить метаданные на сайт Box.com.

1. Добавьте Box из корпоративных приложений.

2. Настройка единого входа

  ![Настройте единый вход в Box.](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. Во-первых, убедитесь, что на портале управления Azure соответствующим образом задан URL-адрес входа для Box. Это URL-адрес вашего клиента Box.com. Он должен соответствовать следующему формату: https://.box.com.

4. Идентификатор для этого приложения не применяется, но он по-прежнему отображается как обязательное поле. Просто учитывайте этот факт.

5. Идентификатор пользователя должен быть user.mail (чтобы обеспечить единый вход для гостевых учетных записей).

6. Создайте новый сертификат SAML.

7. Чтобы приступить к настройке клиента Box.com и использовать Azure Active Directory в качестве поставщика удостоверений, сначала скачайте следующий файл метаданных и сохраните его локально на своем компьютере: скачать файл метаданных (убедитесь, что он активен).

8. Передайте этот файл метаданных в группу поддержки Box. Группа поддержки осуществляет настройку единого входа.

9. Выполните настройки, необходимые для автоматической подготовки пользователей.

  ![Авторизация Azure AD для подключения к Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Приглашенные также должны активировать свои приглашения от приложения Box.

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md)
* [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory](active-directory-b2b-claims-mapping.md)
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



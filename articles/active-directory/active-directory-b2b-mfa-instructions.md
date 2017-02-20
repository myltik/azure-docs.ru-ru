---
title: "Многофакторная идентификация пользователей службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает Многофакторную идентификацию (MFA), которая позволяет предоставлять выборочный доступ к корпоративным приложениям."
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
ms.date: 02/07/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 049f8ecec38a518276c6deea6a285a59d1401de6
ms.openlocfilehash: 318ec4a2b807aaed5c61272016d0957d6e6f5004


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Многофакторная идентификация пользователей службы совместной работы Azure Active Directory B2B

В данном обновлении общедоступной предварительной версии службы совместной работы Azure AD B2B мы представляем возможность для организаций также применять политики Многофакторной идентификации (MFA) по отношению к пользователям службы совместной работы B2B. В этом обновлении MFA всегда применяется на уровне клиента ресурса.

Это означает следующее:
1. Администратор или информационный работник компании А приглашает пользователя из компании Б в приложение Foo, принадлежащее компании A.
2. При доступе в приложение *Foo*, принадлежащее компании A, настроено обязательное прохождение MFA.
3. Когда пользователь из компании Б пытается получить доступ к приложению Foo из клиента компании А, то ему предлагается пройти Многофакторную идентификацию согласно требованиям политики MFA компании А.
4. Пользователь может настроить параметры прохождения MFA, согласованные с компанией А.
5. Эти настройки будут действительны для любого идентификатора (например, Azure AD или MSA, если пользователи из компании Б используют для аутентификации внешние идентификаторы).
6. У компании А должны быть номера SKU Azure AD уровня "Премиум", поддерживающие MFA. Пользователь из компании Б будет использовать эту лицензию компании А.
7. Таким образом, ответственность за применение MFA по отношению к пользователям службы совместной работы B2B из партнерской организации *всегда* лежит на приглашающем клиенте, а не на партнерской организации (даже если она имеет возможности применения MFA). В будущих выпусках будет реализована возможность для приглашающей организации "доверять" проверке MFA определенной партнерской организации вместо применения собственной проверки MFA.

## <a name="setting-up-mfa-for-b2b-users"></a>Настройка MFA для пользователей службы B2B
Чтобы узнать, как можно с легкостью настроить MFA для пользователей службы совместной работы B2B, просмотрите [этот видеоролик](https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup).

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Прохождение MFA пользователями службы B2B при активации предложения
В [этом видеоролике](https://channel9.msdn.com/Blogs/Azure/MFA-redemption) показано, как выглядит процедура активации.

## <a name="mfa-reset-for-b2b-collaboration-users"></a>Сброс настроек MFA для пользователей службы совместной работы B2B
В настоящее время администратор может потребовать от пользователей службы совместной работы B2B пройти повторную проверку только с помощью приведенных ниже командлетов PowerShell. Поэтому следует использовать эти командлеты, когда требуется сбросить настройки метода проверки для пользователя службы совместной работы B2B.

> [!NOTE]
> Для использования новых командлетов необходимо установить модуль Azure AD PowerShell V2, который можно скачать по этому адресу: https://www.powershellgallery.com/packages/AzureADPreview

1. Подключение к Azure AD

  ```
  Connect-MsolService and login
  ```
2. Получение сведений о методах проверки для всех пользователей.

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Пример:

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Сброс настроек метода MFA для определенного пользователя. Затем можно использовать полученное имя участника-пользователя (UserPrincipalName) для выполнения команды сброса настроек. Эта команда потребует от пользователя службы совместной работы B2B повторно настроить методы проверки. Пример:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-add-guest-to-role.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->



---
title: "Требования к данным для самостоятельного сброса пароля Azure AD | Документация Майкрософт"
description: "Требования к данным для самостоятельного сброса пароля Azure AD и информация о том, как их выполнить"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: ed82200bf81702bbe35a371e7d86676c2c27d8f4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Развертывание сброса пароля без регистрации пользователя

Для развертывания функции самостоятельного сброса пароля (SSPR) в Azure Active Directory (Azure AD) нужно указать данные проверки подлинности. В некоторых организациях пользователи вводят данные проверки подлинности самостоятельно. Но в большинстве организаций используется синхронизация существующих данных в Active Directory. Синхронизированные данные становятся доступными для Azure AD и функции самостоятельного сброса пароля без участия пользователя, если сделать следующее:
   * правильно отформатировать данные в локальном каталоге;
   * настроить [Azure AD Connect, используя стандартные параметры](./connect/active-directory-aadconnect-get-started-express.md).

Для правильной работы номера телефонов должны быть указаны в формате *+код_страны номер_телефона*. Например, +1 4255551234.

> [!NOTE]
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

## <a name="fields-populated"></a>Заполненные поля

Если вы используете параметры по умолчанию, в Azure AD Connect выполняются следующие сопоставления.

| Локальная служба Active Directory | Azure AD | Контактная информация для проверки подлинности в Azure AD |
| --- | --- | --- |
| TelephoneNumber | Рабочий телефон | Дополнительный телефон |
| mobile | Мобильный телефон | Номер телефона |


## <a name="security-questions-and-answers"></a>Контрольные вопросы и ответы на них

Контрольные вопросы и ответы на них надежно хранятся в клиенте Azure AD. Эти данные доступны пользователям только на [портале регистрации SSPR](https://aka.ms/ssprsetup). Администраторы не могут видеть или изменять содержимое вопросов и ответов других пользователей.

### <a name="what-happens-when-a-user-registers"></a>Что происходит, когда пользователь проходит регистрацию?

Когда пользователь регистрируется, на странице регистрации будут заполнены следующие поля:

* **Телефон для проверки подлинности**;
* **Адрес электронной почты для проверки подлинности**;
* **Security Questions and Answers** (Контрольные вопросы и ответы на них).

Если вы указали значения для полей **Мобильный телефон** или **Запасной адрес электронной почты**, пользователи могут использовать их для сброса паролей, даже если они еще не прошли регистрацию в службе. Кроме того, эти значения будут отображаться для пользователей при первой регистрации, и они смогут изменить их при необходимости. После успешной регистрации эти значения будут храниться в полях **Телефон для проверки подлинности** и **Адрес электронной почты для проверки подлинности** соответственно.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Установка и чтение данных проверки подлинности с помощью PowerShell

С помощью PowerShell можно заполнить следующие поля:

* **Запасной адрес электронной почты**;
* **Мобильный телефон**;
* **Рабочий телефон** — его можно указать, только если это значение не синхронизируется с локальным каталогом.

### <a name="use-powershell-version-1"></a>Использование PowerShell версии 1

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). После его установки вы можете выполнить следующие шаги по настройке каждого поля.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Настройка данных проверки подлинности с помощью PowerShell версии 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Считывание данных проверки подлинности с помощью PowerShell версии 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Считывание параметров "Телефон для проверки подлинности" и "Адрес электронной почты для проверки подлинности"

Для считывания **телефона для проверки подлинности** и **адреса электронной почты для проверки подлинности** при использовании PowerShell версии 1 выполните следующие команды:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Использование PowerShell версии 2

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell версии 2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). После его установки вы можете выполнить следующие шаги по настройке каждого поля.

Чтобы быстро установить одну из последних версий PowerShell, поддерживающую Install-Module, выполните приведенные ниже команды. (Первая строка проверяет, установлен ли модуль.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Настройка данных проверки подлинности с помощью PowerShell версии 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Считывание данных проверки подлинности с помощью PowerShell версии 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть самостоятельный сброс пароля](active-directory-passwords-best-practices.md)
* [Сброс или изменение пароля](active-directory-passwords-update-your-own-password.md)
* [Регистрация для самостоятельного сброса пароля](active-directory-passwords-reset-register.md)
* [Требования к лицензированию самостоятельного сброса пароля в Azure AD](active-directory-passwords-licensing.md)
* [Доступные пользователям методы проверки подлинности](active-directory-passwords-how-it-works.md#authentication-methods)
* [Параметры политики для SSPR](active-directory-passwords-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](active-directory-passwords-writeback.md)
* [Как сообщать о действиях в SSPR](active-directory-passwords-reporting.md)
* [Обзор всех параметров SSPR и их значение](active-directory-passwords-how-it-works.md)
* [Как устранить неполадки самостоятельного сброса пароля](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)

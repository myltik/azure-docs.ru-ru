---
title: Требования к данным для самостоятельного сброса пароля Azure AD | Документация Майкрософт
description: Требования к данным для самостоятельного сброса пароля Azure AD и информация о том, как их выполнить
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5409bf198d0e3f6537619ef4698d9f2e31bd27c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257594"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Развертывание сброса пароля без регистрации пользователя

Для развертывания функции самостоятельного сброса пароля (SSPR) в Azure Active Directory (Azure AD) нужно указать данные проверки подлинности. В некоторых организациях пользователи вводят данные проверки подлинности самостоятельно. Но в большинстве организаций используется синхронизация существующих данных в Active Directory. Синхронизированные данные становятся доступными для Azure AD и функции самостоятельного сброса пароля без участия пользователя, если сделать следующее:
   * правильно отформатировать данные в локальном каталоге;
   * настроить [Azure AD Connect, используя стандартные параметры](./../connect/active-directory-aadconnect-get-started-express.md).

Для правильной работы номера телефонов должны быть указаны в формате *+код_страны номер_телефона*. Например, +1 4255551234.

> [!NOTE]
> Между кодом страны и номером телефона должен быть пробел.
>
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

## <a name="fields-populated"></a>Заполненные поля

Если вы используете параметры по умолчанию, в Azure AD Connect выполняются следующие сопоставления.

| Локальная служба Active Directory | Azure AD |
| --- | --- |
| TelephoneNumber | Рабочий телефон |
| mobile | Мобильный телефон |

Когда пользователь подтвердит номер мобильного телефона, этот номер также отобразится в поле "Телефон" в разделе контактных данных для проверки подлинности в Azure AD.

## <a name="authentication-contact-info"></a>Контактная информация для проверки подлинности

Глобальный администратор может вручную задать контактные данные пользователя для проверки подлинности, как показано на следующем снимке экрана.

![Контакт][Contact]

Если в поле "Телефон" указан номер и использование мобильного телефона предусмотрено в политике SSPR, пользователь увидит этот номер на странице регистрации для сброса пароля, а также во время сброса пароля. 

Поле "Альтернативный телефон" при сбросе пароля не используется.

Если в поле "Электронная почта" указан адрес и использование электронной почты предусмотрено в политике SSPR, пользователь увидит этот адрес на странице регистрации для сброса пароля, а также во время сброса пароля.

Если в поле "Альтернативный адрес электронной почты" указан адрес и использование электронной почты предусмотрено в политике SSPR, пользователь **не** увидит этот адрес на странице регистрации для сброса пароля, но увидит его во время сброса пароля. 


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

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell версии 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). После его установки вы можете выполнить следующие шаги по настройке каждого поля.

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

## <a name="next-steps"></a>Дополнительная информация

* [Как развернуть самостоятельный сброс пароля?](howto-sspr-deployment.md)
* [Сброс или изменение пароля](../active-directory-passwords-update-your-own-password.md)
* [Регистрация для самостоятельного сброса пароля](../active-directory-passwords-reset-register.md)
* [Требования к лицензированию самостоятельного сброса пароля в Azure AD](concept-sspr-licensing.md)
* [Доступные пользователям методы проверки подлинности](concept-sspr-howitworks.md#authentication-methods)
* [Параметры политики для SSPR](concept-sspr-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](howto-sspr-writeback.md)
* [Как сообщать о действиях в SSPR](howto-sspr-reporting.md)
* [Обзор всех параметров SSPR и их значение](concept-sspr-howitworks.md)
* [Как устранить неполадки самостоятельного сброса пароля](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Глобальные администраторы могут изменять контактные данные для аутентификации пользователя"

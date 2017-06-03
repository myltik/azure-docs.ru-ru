---
title: "Требования к данным для самостоятельного сброса пароля Azure AD | Документация Майкрософт"
description: "Требования к данным для самостоятельного сброса пароля Azure AD и информация о том, как их выполнить"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.editor: gahug
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: f7ecb22ee46d83867453e035f8a639bc7f7f2d81
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Развертывание сброса пароля без регистрации пользователя

Для развертывания функции самостоятельного сброса пароля нужно указать данные аутентификации. Хотя в некоторых организациях пользователи вводят свои данные аутентификации самостоятельно, в других используется синхронизация существующих данных в Active Directory. Если правильно отформатировать данные в локальном каталоге и настроить [Azure AD Connect, используя стандартные параметры](./connect/active-directory-aadconnect-get-started-express.md), эти данные становятся доступным для Azure AD и функции самостоятельного сброса паролей без участия пользователя.

Для правильной работы все номера телефонов должны быть в формате "+код страны – номер телефона". Например: +1 4255551234.

> [!NOTE]
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

## <a name="fields-populated"></a>Заполненные поля

Если вы используете параметры по умолчанию, в Azure AD Connect выполняются следующие сопоставления.

| Локальная служба AD | Azure AD | Контактные данные проверки подлинности Azure AD |
| --- | --- | --- |
| TelephoneNumber | Рабочий телефон | Дополнительный телефон |
| mobile | Мобильный телефон | Номер телефона |


## <a name="security-questions-and-answers"></a>Контрольные вопросы и ответы на них

Контрольные вопросы и ответы на них надежно хранятся в клиенте Azure AD. Эти данные доступны пользователям только на [портале регистрации SSPR](https://aka.ms/ssprsetup). Администраторы не могут видеть или изменять содержимое вопросов и ответов других пользователей.

### <a name="what-happens-when-a-user-registers"></a>Что происходит, когда пользователь проходит регистрацию?

Когда пользователь регистрируется, на странице регистрации будут заполнены следующие поля:

* Телефон для проверки подлинности
* Адрес электронной почты для проверки подлинности
* Контрольные вопросы и ответы на них

Если вы указали значения для полей **Мобильный телефон** или **Запасной адрес электронной почты**, пользователи могут использовать их для сброса паролей, даже если они еще не прошли регистрацию в службе. Кроме того, эти значения будут отображаться для пользователей при первой регистрации, и они смогут изменить их при необходимости. После успешной регистрации эти значения будут храниться в полях **Телефон для проверки подлинности** и **Адрес электронной почты для проверки подлинности** (их нельзя будет изменить).

## <a name="set-and-read-authentication-data-using-powershell"></a>Установка и считывание данных аутентификации с помощью PowerShell

С помощью PowerShell можно заполнить следующие поля

* Запасной адрес электронной почты
* Мобильный телефон
* Рабочий телефон — его можно указать, только если это значение не синхронизируется с локальным каталогом

### <a name="using-powershell-v1"></a>Использование PowerShell V1

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). После его установки вы можете выполнить следующие процедуры по настройке каждого поля.

#### <a name="set-authentication-data-with-powershell-v1"></a>Настройка данных аутентификации с помощью PowerShell V1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Чтение данных аутентификации с помощью PowerShell V1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>Телефон и адрес электронной почты для аутентификации можно прочитать только с помощью следующих команд PowerShell V1:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>Использование PowerShell V2

Чтобы начать работу, необходимо [скачать и установить модуль Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). После его установки вы можете выполнить следующие процедуры по настройке каждого поля.

Чтобы быстро установить одну из последних версий PowerShell, поддерживающую Install-Module, выполните приведенные ниже команды (первая строка просто проверяет наличие PowerShell).

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>Настройка данных аутентификации с помощью PowerShell V2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>Чтение данных аутентификации с помощью PowerShell V2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сбросе пароля с помощью Azure AD см. в следующих источниках:

* [**Быстрое начало работы с самостоятельным сбросом пароля в Azure AD**](active-directory-passwords-getting-started.md). Запуск и выполнение службы самостоятельного управления паролями Azure AD. 
* [**Licensing requirements for Azure AD self-service password reset**](active-directory-passwords-licensing.md) (Требования к лицензированию самостоятельного сброса пароля в Azure AD). Сведения о настройке лицензирования Azure AD.
* [**Развертывание функции сброса паролей для пользователей**](active-directory-passwords-best-practices.md). Рекомендации по планированию и развертыванию SSPR для пользователей.
* [**Настройка компонентов управления паролями в соответствии с требованиями организации**](active-directory-passwords-customize.md). Сведения о настройке интерфейса и параметров использования SSPR для организации.
* [**Политики и ограничения для паролей в Azure Active Directory**](active-directory-passwords-policy.md). Общие сведения и информация об установке политик паролей Azure AD.
* [**Параметры отчетов для управления паролями Azure AD**](active-directory-passwords-reporting.md). Определяйте, кто и когда использовал функцию SSPR.
* [**Как работает управление паролями в Azure Active Directory**](active-directory-passwords-how-it-works.md). Сведения о принципе работы управления паролями.
* [**Вопросы и ответы об управлении паролями**](active-directory-passwords-faq.md). Как? Почему? Что? Где? Кто? Когда? Здесь приведены ответы на интересующие вас вопросы.
* [**Устранение неполадок, связанных с управлением паролями**](active-directory-passwords-troubleshoot.md). Сведения об устранении распространенных проблем с SSPR.


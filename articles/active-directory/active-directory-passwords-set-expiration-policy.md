---
title: "Установка политик срока действия пароля в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как проверить политики срока действия и изменить срок действия пароля пользователя (одного или сразу нескольких) для Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: f744a29e12fb693dd422f359d0faacaae004351b


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Установка политик срока действия пароля в Azure Active Directory
> [!IMPORTANT]
> **Вы здесь потому, что возникают проблемы при входе?** Если это так, [с помощью этих инструкций можно изменить и сбросить пароль](active-directory-passwords-update-your-own-password.md).
>
>

Глобальный администратор облачной службы Майкрософт Microsoft может использовать модуль Azure Active Directory для Windows PowerShell, чтобы настроить неограниченный срок действия паролей пользователей. Можно также использовать командлеты Windows PowerShell, чтобы удалить бессрочную конфигурацию или просмотреть, какие пользовательские пароли имеют неограниченный срок действия. В этой статье представлена справка по облачным службам, таким как Microsoft Intune и Office 365, которые используют Microsoft Azure Active Directory для служб идентификации и каталогов.

> [!NOTE]
> Неограниченный срок действия можно настроить только для паролей учетных записей пользователей, которые не синхронизируются в процессе синхронизации каталогов. Дополнительные сведения о синхронизации каталогов см. в списке разделов [стратегии синхронизации каталогов](https://msdn.microsoft.com/library/azure/hh967642.aspx).
>
>

Чтобы использовать командлеты Windows PowerShell, сначала необходимо установить их.

## <a name="what-do-you-want-to-do"></a>Что необходимо сделать?
* [Как проверить политику срока действия пароля](#how-to-check-expiration-policy-for-a-password)
* [Задание срока действия пароля](#set-a-password-to-expire)
* [Задание бессрочного пароля](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Как проверить политику срока действия пароля
1. Подключитесь к Windows PowerShell с помощью учетных данных администратора.
2. Выполните одно из следующих действий.

   * Чтобы увидеть, задан ли для одного пользователя бессрочный пароль, выполните следующий командлет, используя имя участника-пользователя (например, aprilr@contoso.onmicrosoft.com)) или идентификатор проверяемого пользователя `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Чтобы просмотреть параметр «Пароль не имеет окончания срока действия» для всех пользователей, выполните следующий командлет: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Задание срока действия пароля
1. Подключитесь к Windows PowerShell с помощью учетных данных администратора.
2. Выполните одно из следующих действий.

   * Чтобы установить пароль одного пользователя со сроком действия, выполните следующий командлет, используя имя участника-пользователя (UPN) или идентификатор пользователя: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Чтобы задать срок действия паролей всех пользователей в организации, используйте следующий командлет: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Установка бессрочного пароля
1. Подключитесь к Windows PowerShell с помощью учетных данных администратора.
2. Выполните одно из следующих действий.

   * Чтобы установить бессрочный пароль для одного пользователя, выполните следующий командлет, используя имя участника-пользователя (UPN) или идентификатор пользователя: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Чтобы установить бессрочные пароли для всех пользователей в организации, выполните следующий командлет: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Дальнейшие действия
* **Вы здесь потому, что возникают проблемы при входе?** Если это так, [с помощью этих инструкций можно изменить и сбросить пароль](active-directory-passwords-update-your-own-password.md).



<!--HONumber=Feb17_HO2-->



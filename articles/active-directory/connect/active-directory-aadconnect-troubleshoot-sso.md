---
title: "Azure AD Connect: устранение неполадок с простым единым входом | Документы Майкрософт"
description: "Этот раздел описывает устранение неполадок с простым единым входом Azure Active Directory (Azure AD)."
services: active-directory
keywords: "что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Порядок для устранения неполадок с простым единым входом Azure Active Directory

## <a name="known-issues"></a>Известные проблемы

- Если вы синхронизируете с помощью Azure AD Connect больше 30 лесов AD, мастер, используемый для настройки простого единого входа, работает неправильно. Чтобы избежать этого, можно [вручную включить](#manual-reset-of-azure-ad-seamless-sso) функцию простого единого входа на клиенте.
- Добавление URL-адресов службы Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) в зону "Надежные сайты" вместо зоны "Местная интрасеть".

## <a name="troubleshooting-checklist"></a>Контрольный список по устранению неполадок

Используйте следующий контрольный список для устранения неполадок с простым единым входом Azure AD:

1. Проверьте, включена ли функция простого единого входа на клиенте в инструменте Azure AD Connect. Если функцию не удается включить (например, из-за заблокированного порта), убедитесь, что выполнены все [предварительные условия](active-directory-aadconnect-sso.md#pre-requisites). Если проблемы с включением функции не исчезли, обратитесь в службу поддержки Майкрософт.
2. Оба URL-адреса службы (https://autologon.microsoftazuread-sso.com и https://aadg.windows.net.nsatc.net) должны быть определены в зоне интрасети.
3. Убедитесь, что корпоративный компьютер присоединен к домену AD.
4. Убедитесь, что пользователь вошел на компьютер с помощью доменной учетной записи AD.
5. Убедитесь, что учетная запись пользователя получена из леса AD, где настроен простой единый вход.
6. Убедитесь, что компьютер подключен к корпоративной сети.
7. Убедитесь, что время на компьютере синхронизировано с временем в Active Directory, и разница между временем контроллеров домена не превышает 5 минут.
8. Удалите существующие билеты Kerberos с компьютера этой службы. Это можно сделать, выполнив команду **klist purge** в командной строке. Билеты Kerberos пользователей обычно действительны в течение 12 часов. Обратите внимание, что соответствующий параметр в Active Directory может быть настроен иначе.
9. Просмотрите журналы консоли браузера (в разделе "Средства разработчика"), чтобы выявить возможные проблемы.
10. Просмотрите также [Журналы контроллеров домена](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Журналы контроллеров домена

Если в контроллере домена включен аудит успешных операций, при каждом входе пользователя с помощью простого единого входа в журнал событий добавляется запись системы безопасности (событие 4769, связанное с учетной записью компьютера **AzureADSSOAcc$**). Эти события безопасности можно найти с помощью приведенного ниже запроса.

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Ручной сброс простого единого входа Azure AD

Если устранение неполадок не помогает, выполните следующие действия, чтобы вручную сбросить или включить эту функцию на клиенте:

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Импорт модуля PowerShell для простого единого входа

- Для начала скачайте и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Затем скачайте и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
- Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2) Получение списка лесов AD, где включен простой единый вход

- В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных администратора клиента Azure AD.
- Вызовите `Get-AzureADSSOStatus`. Вы получите список лесов AD (см. список "Домены"), где эта функция включена.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. Отключение простого единого входа для каждого леса AD, где эта функция была включена

- В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных администратора клиента Azure AD.
- Вызовите `$creds = Get-Credential`. Появится всплывающее окно для ввода учетных данных администратора домена для соответствующего леса AD.
- Вызовите `Disable-AzureADSSOForest -OnPremCredentials $creds`. Это позволяет удалить учетную запись компьютера AZUREADSSOACCT из локального контроллера домена, а также отключить эту функцию для данного конкретного леса AD.
- Повторите описанные выше действия для каждого леса AD, где включена эта функция.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. Включение простого единого входа для каждого леса AD

- Вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных администратора клиента Azure AD.
- Вызовите `Enable-AzureADSSOForest`. Появится всплывающее окно для ввода учетных данных администратора домена для соответствующего леса AD.
- Повторите описанные выше действия для каждого леса AD, где нужно включить эту функцию.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. Включение простого единого входа на клиенте

- Вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных администратора клиента Azure AD.
- Вызовите `Enable-AzureADSSO` и введите "true" в приглашении `Enable: `, чтобы включить эту функцию на клиенте.


---
title: "Azure AD Connect: устранение неполадок в работе сквозной аутентификации | Документация Майкрософт"
description: "В этой статье описывается устранение неполадок в работе сквозной аутентификации Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Устранение неполадок в работе сквозной аутентификации Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Как устранять неполадки в работе сквозной аутентификации Azure Active Directory

Эта статья поможет найти сведения об устранении распространенных проблем во время установки, регистрации или удаления соединителей сквозной аутентификации, устанавливаемых с помощью Azure AD Connect, или изолированных соединителей. А также во время включения и использования функции сквозной аутентификации Azure Active Directory (Azure AD) на клиенте.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Проблемы при установке соединителей, устанавливаемых с помощью Azure AD Connect, или изолированных соединителей

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Соединитель прокси приложения Azure AD уже установлен

Соединитель сквозной аутентификации невозможно установить на одном сервере с соединителем [прокси приложения Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). Потребуется установить соединитель сквозной аутентификации на отдельном сервере.

### <a name="an-unexpected-error-occured"></a>Произошла непредвиденная ошибка

[Соберите журналы соединителя](#collecting-pass-through-authentication-connector-logs) на сервере и обратитесь в службу поддержки Майкрософт с этой проблемой.

## <a name="issues-during-registration-of-connectors"></a>Проблемы во время регистрации соединителей

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>Зарегистрировать соединитель не удалось из-за заблокированных портов

Убедитесь, что сервер, на котором установлен соединитель, может взаимодействовать с URL-адресами и портами нашей службы, перечисленными [здесь](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>Сбой регистрации соединителя из-за ошибок проверки подлинности учетной записи или токена

Вы должны использовать облачную учетную запись глобального администратора для установки и регистрации изолированного соединителя или Azure AD Connect. Существует известная проблема с учетными записями глобального администратора с поддержкой Многофакторной идентификации. В качестве обходного решения временно отключите Многофакторную идентификацию (только чтобы завершить операции).

### <a name="an-unexpected-error-occurred"></a>Произошла непредвиденная ошибка

[Соберите журналы соединителя](#collecting-pass-through-authentication-connector-logs) на сервере и обратитесь в службу поддержки Майкрософт с этой проблемой.

## <a name="issues-during-uninstallation-of-connectors"></a>Проблемы во время удаления соединителей

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Предупреждение при удалении Azure AD Connect

Если вы включили сквозную аутентификацию на клиенте и пытаетесь удалить Azure AD Connect, то появится следующее предупреждение: "Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers" (Пользователи не смогут войти в Azure AD, если на других серверах не установлены другие агенты сквозной аутентификации).

Необходимо установить [высокодоступную](active-directory-aadconnect-pass-through-authentication.md) конфигурацию, прежде чем удалять Azure AD Connect, чтобы избежать нарушения входа пользователей.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Проблемы при включении функции сквозной аутентификации

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>Не удалось включить функцию из-за отсутствия доступных соединителей

У вас должен быть хотя бы один активный соединитель, чтобы включить сквозную аутентификацию на клиенте. Вы можете установить соединитель с помощью установки Azure AD Connect или изолированного соединителя.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>Не удалось включить функцию из-за заблокированных портов

Убедитесь, что сервер, на котором установлена служба Azure AD Connect, может взаимодействовать с URL-адресами и портами нашей службы, перечисленными [здесь](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Сбой включения функции из-за ошибок проверки подлинности учетной записи или токена

При включении функции вы должны использовать только облачную учетную запись глобального администратора. Существует известная проблема с учетными записями глобального администратора с поддержкой MFA. В качестве обходного решения временно отключите Многофакторную идентификацию (только чтобы завершить операции).

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Проблемы в работе функции сквозной аутентификации

### <a name="user-facing-sign-in-errors"></a>Ошибки при входе пользователей

Функция выводит на экран входа в Azure AD следующие ошибки пользователей. Они подробно описаны ниже, как и соответствующие действия по устранению.

|Ошибка|Description (Описание)|Способы устранения:
| --- | --- | ---
|AADSTS80001|Не удалось подключиться к Active Directory|Убедитесь, что серверы соединителей являются членами того же леса, что и пользователи, чьи пароли должны быть проверены, и могут подключиться к Active Directory.  
|AADSTS8002|Истекло время ожидания подключения к Active Directory|Убедитесь, что служба Active Directory доступна и отвечает на запросы соединителей.
|AADSTS80004|Соединителю передано недопустимое имя пользователя|Убедитесь, что пользователь пытается войти в систему с правильным именем пользователя.
|AADSTS80005|При проверке было обнаружено непредсказуемое исключение WebException|Скорее всего, это временная ошибка. Повторите запрос. Если проблема не исчезла, обратитесь в службу поддержки Майкрософт.
|AADSTS80007|Произошла ошибка при взаимодействии с Active Directory|Проверьте журналы соединителя для получения дополнительных сведений и убедитесь, что Active Directory работает, как ожидалось.

## <a name="collecting-pass-through-authentication-connector-logs"></a>Сбор журналов соединителя сквозной аутентификации

В зависимости от типа возникшей проблемы необходимо будет искать журналы соединителя сквозной аутентификации в разных расположениях.

### <a name="connector-event-logs"></a>Журналы событий соединителя

Ошибки, связанные с соединителем, можно просмотреть в приложении "Просмотр событий" на сервере. Они находятся в папке **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Чтобы получить подробные журналы анализа и отладки, можно включить журнал "Сеанс". Не включайте этот журнал во время обычной работы соединителя. Используйте его только для устранения неполадок. Обратите внимание, что содержимое этого журнала отображаются только после его отключения.

### <a name="detailed-trace-logs"></a>Подробные журналы трассировки

Чтобы устранить неполадки при входе пользователей, найдите журналы трассировки в папке **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. В этих журналах содержатся причины сбоев, возникших при входе пользователей с помощью сквозной аутентификации. Ниже приведен пример записи журнала.

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Описание ошибки (1328 в приведенном выше примере) можно получить, открыв окно командной строки и выполнив следующую команду. Примечание. Необходимо будет заменить число 1328 номером ошибки, который отображается в ваших журналах.

`Net helpmsg 1328`

Результат должен иметь следующий вид.

![Сквозная проверка подлинности](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Журналы контроллеров домена

Если включено ведение журнала аудита, то дополнительные сведения можно найти в журналах безопасности контроллеров домена. Ниже показан простой способ извлечения запросов на вход, отправленных соединителями сквозной аутентификации.

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```


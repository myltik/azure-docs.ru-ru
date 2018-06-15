---
title: 'Azure AD Connect: устранение неполадок подключения | Документация Майкрософт'
description: Сведения об устранении неполадок подключения в Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c12630deb45fd057537c42157d88fdeef22d18b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593019"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Устранение неполадок подключения в Azure AD Connect
В этой статье рассказывается, как работает подключение между Azure AD Connect и Azure AD и как устранять неполадки подключения. Как правило, проблемы возникают в среде с прокси-сервером.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Устранение неполадок подключения в мастере установки
Azure AD Connect использует для аутентификации современную проверку подлинности (с использованием библиотеки ADAL). Поскольку речь идет о двух приложениях .NET, мастер установки и модуль синхронизации требуют правильной настройки файла machine.config.

В этой статье показано, как Fabrikam подключается к Azure AD через прокси-сервер. Прокси-сервер имеет имя fabrikamproxy и использует порт 8080.

Во-первых, проверим правильность настройки файла [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) .  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> В некоторых сторонних блогах говорится, что вместо него изменения необходимо вносить в файл miiserver.exe.config. Однако при каждом обновлении этот файл перезаписывается, так что даже если при первичной установке все работает, то с первым же обновлением система работать перестанет. По этой причине рекомендуется обновлять файл machine.config.
>
>

На прокси-сервере должен быть также открыт необходимый URL-адрес. Официальный список см. в статье [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Для этих URL-адресов в приведенной ниже таблице показаны минимальные условия, которые должны соблюдаться для подключения к Azure AD. Этот список не включает дополнительные функции, такие как обратная запись паролей или Azure AD Connect Health. Он предназначен только для поиска и устранения неполадок, связанных с начальной конфигурацией.

| URL-адрес | Порт | ОПИСАНИЕ |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Используется для загрузки списков CRL. |
| \*.verisign.com |HTTP/80 |Используется для загрузки списков CRL. |
| \*.entrust.com |HTTP/80 |Используется для загрузки списков CRL для MFA. |
| \*.windows.net |HTTPS/443 |Используется для входа в Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Используется для многофакторной проверки подлинности (MFA). |
| \*.microsoftonline.com |HTTPS/443 |Используется для настройки каталога Azure AD, а также импорта и экспорта данных. |

## <a name="errors-in-the-wizard"></a>Ошибки в мастере
Мастер установки использует два различных контекста безопасности. На странице **Подключение к Azure AD** он использует имя пользователя, выполнившего вход. На странице **Настройка** он переключается на [учетную запись, под которой работает служба модуля синхронизации](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Если возникают проблемы, то обычно они проявляются уже на странице мастера **Подключение к Azure AD**, так как конфигурация прокси-сервера является глобальной.

Ниже приведены наиболее распространенные ошибки, которые встречаются в мастере установки.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Неправильно настроен мастер установки
Эта ошибка появляется в случае, если мастер не может связаться с прокси-сервером.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Если возникает эта ошибка, проверьте конфигурацию в файле [machine.config](active-directory-aadconnect-prerequisites.md#connectivity).
* Если конфигурация выглядит нормально, выполните действия, описанные в разделе [Проверка подключения прокси-сервера](#verify-proxy-connectivity) , и убедитесь в том, что проблема возникает не только в мастере.

### <a name="a-microsoft-account-is-used"></a>Используется учетная запись Майкрософт
Если использовать **учетную запись Майкрософт** вместо **учебной или рабочей учетной записи**, то возникнет общая ошибка.  
![Используется учетная запись Майкрософт](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Не удается связаться с конечной точкой многофакторной проверки подлинности
Эта ошибка отображается в том случае, если не удается связаться с конечной точкой **https://secure.aadcdn.microsoftonline-p.com** и глобальный администратор включил Многофакторную идентификацию (MFA).  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Если вы видите эту ошибку, то убедитесь, что конечная точка **secure.aadcdn.microsoftonline-p.com** добавлена на прокси-сервер.

### <a name="the-password-cannot-be-verified"></a>Невозможно проверить пароль
Если мастер установки успешно подключается к Azure AD, но проверить пароль невозможно, то отображается следующая ошибка:  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* Проверьте, не используется ли временный пароль, который необходимо сменить. Проверьте, правильно ли указан пароль. Попробуйте войти в систему по адресу https://login.microsoftonline.com (на другом компьютере, отличном от сервера Azure AD Connect), и убедитесь, что учетная запись доступна.

### <a name="verify-proxy-connectivity"></a>Проверка подключения прокси-сервера
Для проверки возможности подключения сервера Azure AD Connect к прокси-серверу и Интернету можно использовать некоторые командлеты PowerShell, показывающие, пропускает ли прокси-сервер веб-запросы. В командной строке PowerShell выполните командлет `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (С технической точки зрения первый вызов направляется по адресу https://login.microsoftonline.com, и этот URI также будет работать, хотя другие URI отвечают быстрее.)

Для связи с прокси-сервером PowerShell использует конфигурацию в файле machine.config. Параметры в winhttp/netsh не должны влиять на эти командлеты.

Если прокси-сервер настроен правильно, то вы должны получить успешное состояние: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png).

Если отображается сообщение **Невозможно соединиться с удаленным сервером**, то это означает, что PowerShell пытается передать прямой вызов, не используя прокси-сервер, или неправильно настроен DNS. Проверьте правильность настройки файла **machine.config** .
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Если прокси-сервер настроен неправильно, то появляется ошибка: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png).

| Ошибка | Текст сообщения об ошибке | Комментарий |
| --- | --- | --- |
| 403 |Запрещено |Прокси-сервер не был открыт для запрошенного URL-адреса. Проверьте конфигурацию прокси-сервера и убедитесь, что [URL-адреса](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) открыты. |
| 407 |Требуется проверка подлинности прокси-сервера |Для прокси-сервера требуется имя входа, которое не было указано. Если прокси-сервер требует аутентификации, то убедитесь, что этот параметр настроен в файле machine.config. Также убедитесь, что используются учетные записи домена для пользователя, который работает с мастером, а также учетная запись службы. |

### <a name="proxy-idle-timeout-setting"></a>Параметр времени ожидания прокси-сервера в режиме простоя
Когда Azure AD Connect отправляет запрос на экспорт в службу Azure AD, службе Azure AD может потребоваться около 5 минут на обработку запроса и создание ответа. Это особенно актуально в случаях, когда один запрос на экспорт содержит несколько объектов групп с большим количеством участников. Убедитесь, что время ожидания прокси-сервера в режиме простоя превышает 5 минут. В противном случае на сервере Azure AD Connect могут наблюдаться перебои с подключением к Azure AD.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Шаблон взаимодействия между Azure AD Connect и Azure AD
Если вы выполнили все описанные выше действия, а подключение по-прежнему невозможно, то обратитесь к журналам сети. В этом разделе описан нормальный, работающий шаблон подключения, а также перечислены распространенные ложные сигналы, которые при чтении журналов сети можно игнорировать.

* Есть вызовы по адресу https://dc.services.visualstudio.com. Для успешной установки открытие этого URL-адреса в прокси-сервере не требуется, и эти вызовы можно игнорировать.
* Вы заметите, что в разрешении DNS указано, что фактически узлы находятся в пространстве DNS-имен nsatc.net и других, а не в microsoftonline.com. Фактические имена серверов в запросах к веб-службам не указываются, и добавлять эти URL-адреса в прокси-сервер не нужно.
* Конечные точки adminwebservice и provisioningapi представляют собой конечные точки обнаружения и используются для поиска фактически используемой конечной точки. Выбор этих конечных точек зависит от вашего региона.

### <a name="reference-proxy-logs"></a>Справочные журналы прокси-сервера
Приведем дамп из действительного журнала прокси-сервера и страницу мастера установки, с которой он был взят (дублирующиеся записи, относящиеся к одной и той же конечной точке, были удалены). Этот раздел можно использовать как образец для собственных журналов прокси-сервера и сети. В вашей среде конечные точки могут отличаться (особенно URL-адреса, выделенные *курсивом*).

**Подключение к Azure AD**

| Время | URL-адрес |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Настройка**

| Время | URL-адрес |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Первоначальная синхронизация**

| Время | URL-адрес |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Ошибки проверки подлинности
В этом разделе описываются ошибки, которые могут возвращаться ADAL (библиотекой аутентификации, используемой службой Azure AD Connect) и PowerShell. Объяснение ошибки поможет понять причины ее возникновения и определить дальнейшие действия.

### <a name="invalid-grant"></a>Недопустимый набор прав
Недопустимое имя пользователя или пароль. Дополнительные сведения см. в разделе [Невозможно проверить пароль](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Неизвестный тип пользователя
Невозможно найти или разрешить каталог Azure AD. Возможно, при попытке входа в систему вы используете имя пользователя в непроверенном домене?

### <a name="user-realm-discovery-failed"></a>Не удалось выполнить обнаружение области пользователя
Проблемы конфигурации сети или прокси-сервера. Сеть недоступна. Ознакомьтесь с разделом [Устранение неполадок подключения в мастере установки](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Срок действия пароля пользователя истек
Срок действия ваших учетных данных истек. Измените пароль.

### <a name="authorizationfailure"></a>AuthorizationFailure
Неизвестная проблема.

### <a name="authentication-cancelled"></a>Проверка подлинности отменена
Запрос многофакторной проверки подлинности (MFA) был отменен.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Проверка подлинности прошла успешно, но есть проблема с проверкой подлинности в Azure AD PowerShell.

### <a name="azurerolemissing"></a>AzureRoleMissing
Проверка подлинности прошла успешно. Вы не являетесь глобальным администратором.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Проверка подлинности прошла успешно. Было включено управление привилегированными пользователями, и в настоящее время вы не являетесь глобальным администратором. Дополнительные сведения см. в статье [Приступая к работе с управлением привилегированными пользователями Azure AD](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Проверка подлинности прошла успешно. Не удалось получить от Azure AD сведения об организации.

### <a name="retrievedomains"></a>RetrieveDomains
Проверка подлинности прошла успешно. Не удалось получить от Azure AD сведения о домене.

### <a name="unexpected-exception"></a>Неожиданное исключение
Отображается как непредвиденная ошибка в мастере установки. Она может возникнуть при попытке использовать **учетную запись Майкрософт** вместо **учебной или рабочей учетной записи**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Действия по устранению неполадок для предыдущих версий.
Начиная с версии с номером сборки 1.1.105.0 (выпущенной в феврале 2016 г.) помощник по входу более не используется. Этот раздел и конфигурация больше не требуются, но хранятся для ссылки.

Для работы помощника по единому входу необходимо настроить winhttp. Эту настройку можно сделать с помощью [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Неправильно настроен помощник по входу
Эта ошибка появляется, если помощник по входу не может связаться с прокси-сервером или прокси-сервер не пропускает запрос.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Если возникает эта ошибка, проверьте конфигурацию прокси-сервера в [netsh](active-directory-aadconnect-prerequisites.md#connectivity) и убедитесь в правильности ее настройки.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Если конфигурация выглядит нормально, выполните действия, описанные в разделе [Проверка подключения прокси-сервера](#verify-proxy-connectivity) , и убедитесь в том, что проблема возникает не только в мастере.

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

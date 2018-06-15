---
title: 'Azure AD Connect: простой единый вход — быстрый запуск | Документация Майкрософт'
description: В этой статье описывается, как приступить к работе простым единым входом Azure Active Directory.
services: active-directory
keywords: что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e6bb32c20ca9bb6c514cab462e94018543c3a702
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592618"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Простой единый вход Azure Active Directory — быстрый запуск

## <a name="deploy-seamless-single-sign-on"></a>Развертывание простого единого входа

Простой единый вход Azure Active Directory (Azure AD) автоматически обеспечивает пользователям вход в систему, когда они работают на корпоративных компьютерах, подключенных к корпоративной сети. Данная функция предоставляет пользователям удобный доступ к облачным приложениям и не требует установки каких-либо дополнительных локальных компонентов.

Чтобы развернуть прозрачный единый вход, выполните следующие действия.

## <a name="step-1-check-the-prerequisites"></a>Шаг 1. Проверка соблюдения предварительных требований

Выполните указанные ниже предварительные требования.

* **Настройка сервера Azure AD Connect**. Если в качестве метода входа вы используете [сквозную аутентификацию](active-directory-aadconnect-pass-through-authentication.md), никакие дополнительные проверки предварительных требований не нужны. Если в качестве метода входа вы используете [синхронизацию хэша паролей](active-directory-aadconnectsync-implement-password-hash-synchronization.md) и между Azure AD Connect и Azure AD существует брандмауэр, убедитесь в следующем:
   - Используется Azure AD Connect версии 1.1.644.0 или более поздней версии. 
   - Если брандмауэр или прокси-сервер поддерживает внесение DNS в список разрешений, добавьте в него подключения к URL-адресам **\*msappproxy.net** через порт 443. Если нет, разрешите доступ к [диапазонам IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Список диапазонов IP-адресов обновляется еженедельно. Это предварительное условие применяется только в том случае, если вы включаете функцию. Его выполнение не обязательно для фактического входа пользователя.

    >[!NOTE]
    >В Azure AD Connect версий 1.1.557.0, 1.1.558.0, 1.1.561.0 и 1.1.614.0 есть проблема, связанная с синхронизацией хэшей паролей. Если вы _не_ собираетесь использовать синхронизацию хэшей паролей в сочетании со сквозной аутентификацией, прочитайте [заметки о выпуске Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

* **Настройка учетных данных администратора домена**. Необходимы учетные данные администратора домена для каждого леса Active Directory, который:
    * синхронизируется с Azure AD через Azure AD Connect;
    * содержит пользователей, для которых нужно включить простой единый вход.

## <a name="step-2-enable-the-feature"></a>Шаг 2. Включение компонента

Включите простой единый вход через [Azure AD Connect](active-directory-aadconnect.md).

В случае выполнения новой установки Azure AD Connect выберите [пользовательский путь установки](active-directory-aadconnect-get-started-custom.md). На странице **Вход пользователя** установите флажок **Включить единый вход**.

![Azure AD Connect: страница "Вход пользователя"](./media/active-directory-aadconnect-sso/sso8.png)

Если уже имеется установленный экземпляр Azure AD Connect, выберите страницу **Смена имени пользователя для входа** в Azure AD Connect и нажмите кнопку **Далее**.

![Azure AD Connect: страница "Смена имени пользователя для входа"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Следуйте указаниям мастера, пока не отобразится страница **Включение единого входа**. Укажите учетные данные администратора домена для каждого леса Active Directory, который:
    * синхронизируется с Azure AD через Azure AD Connect;
    * содержит пользователей, для которых нужно включить простой единый вход.

После завершения работы мастера на вашем клиенте будет включен простой единый вход.

>[!NOTE]
> Учетные данные администратора домена не хранятся в Azure AD Connect или в Azure AD. Они используются только для включения функции.

Выполните следующие инструкции, чтобы проверить, правильно ли включен простой единый вход.

1. Войдите в [центр администрирования Azure Active Directory](https://aad.portal.azure.com), используя учетные данные глобального администратора для своего клиента.
2. В области слева выберите **Azure Active Directory**.
3. Выберите **Azure AD Connect**.
4. Убедитесь, что функция **Эффективный единый вход** **включена**.

![Портал Azure: область "Azure AD Connect"](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Шаг 3. Развертывание компонента

Чтобы развернуть компонент для пользователей, необходимо добавить приведенный ниже URL-адрес Azure AD в параметры зоны интрасети с помощью групповой политики Active Directory.

- https://autologon.microsoftazuread-sso.com


Кроме того, необходимо включить параметр политики зоны интрасети (с помощью групповой политики), который называется **Разрешить обновление строки состояния в сценарии**. 

>[!NOTE]
> Следующие инструкции подходят только для Internet Explorer и Google Chrome для Windows (при условии, что этот браузер использует тот же набор URL-адресов доверенных сайтов, что и Internet Explorer). В следующем разделе приведены инструкции по настройке Mozilla Firefox и Google Chrome для Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Зачем нужно изменять параметры зоны интрасети пользователей?

По умолчанию браузер автоматически вычисляет соответствующую зону (Интернета или интрасети) по конкретному URL-адресу. Например, http://contoso/ сопоставляется с зоной интрасети, а http://intranet.contoso.com/ — с зоной Интернета (так как этот URL-адрес содержит точку). Браузеры не будут отправлять билеты Kerberos в облачную конечную точку (как URL-адрес Azure AD), если только ее URL-адрес не добавлен явным образом в зону интрасети в браузере.

### <a name="detailed-steps"></a>Подробные инструкции

1. Откройте редактор "Управление групповыми политиками".
2. Измените групповую политику, которая применяется к некоторым или всем пользователям. В этом примере используется **политика домена по умолчанию**.
3. Выберите **Конфигурация пользователя** > **Административные шаблоны** > **Компоненты Windows** > **Internet Explorer** > **Панель управления браузером** > **Вкладка "Безопасность"**. Выберите **Список назначений зоны для веб-сайтов**.
    ![Единый вход](./media/active-directory-aadconnect-sso/sso6.png)
4. Включите политику и введите следующие значения в диалоговом окне.
   - **Имя значения**. Это URL-адрес Azure AD, на который пересылаются билеты Kerberos.
   - **Значение** (данные). **1** указывает зону интрасети.

    Результат должен выглядеть следующим образом:

    Значение: https://autologon.microsoftazuread-sso.com
  
    Data 1

   >[!NOTE]
   > Если вы хотите запретить отдельным пользователям использовать простой единый вход, например, если эти пользователи выполняют вход на общедоступных киосках, установите для указанных выше параметров значение **4**. Это действие добавляет URL-адрес Azure AD в зону с ограниченным доступом и блокирует все попытки простого единого входа.
   >

5. Нажмите кнопку **ОК**, а затем еще раз нажмите кнопку **ОК**.

    ![Единый вход](./media/active-directory-aadconnect-sso/sso7.png)

6. Выберите **Конфигурация пользователя** > **Административные шаблоны** > **Компоненты Windows** > **Internet Explorer** > **Панель управления браузером** > **Вкладка "Безопасность"** > **Зона интрасети**. Выберите **Разрешить обновление строки состояния в сценарии**.

    ![Единый вход](./media/active-directory-aadconnect-sso/sso11.png)

7. Включите параметр политики и нажмите кнопку **ОК**.

    ![Единый вход](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Рекомендации для браузера

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (все платформы)

Mozilla Firefox не выполняет аутентификацию Kerberos автоматически. Каждый пользователь должен вручную добавить URL-адрес Azure AD в параметры Firefox, выполнив следующие действия:
1. Запустите Firefox и введите `about:config` в адресной строке. Проигнорируйте все отображаемые уведомления.
2. Найдите параметр **network.negotiate-auth.trusted-uris**. Этот параметр выводит список доверенных сайтов в Firefox для проверки подлинности Kerberos.
3. Щелкните его правой кнопкой мыши и выберите **Изменить**.
4. Введите https://autologon.microsoftazuread-sso.com в поле.
5. Нажмите кнопку **ОК** и вновь откройте браузер.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Убедитесь, что компьютер под управлением Mac OS присоединен к Active Directory. Инструкции по присоединению к Active Directory см. в документе [Best Practices for Integrating OS X with Active Directory](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf) (Рекомендации по интеграции OS X с Active Directory).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (все платформы)

Если вы переопределили параметры политики [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) или [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) в своей среде, обязательно добавьте к ним URL-адрес Azure AD (https://autologon.microsoftazuread-sso.com).

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (только Mac OS)

Процедура добавления в список разрешений URL-адреса Azure AD для интегрированной аутентификации для Google Chrome на Mac OS и платформах, отличных от Windows, приведена в [этом списке политик проекта Chromium Project](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist).

Применение сторонних расширений групповой политики Active Directory для развертывания URL-адреса Azure AD для Firefox и Google Chrome на платформе Mac в этой статье не рассматривается.

#### <a name="known-browser-limitations"></a>Известные ограничения браузеров

Простой единый вход не работает в конфиденциальном режиме просмотра в браузерах Firefox и Microsoft Edge, а также в Internet Explorer, если браузер работает в режиме повышенной защиты.

## <a name="step-4-test-the-feature"></a>Шаг 4. Тестирование компонента

Чтобы проверить функцию для конкретного пользователя, убедитесь, что соблюдаются все следующие условия:
  - Пользователь выполняет вход на корпоративном устройстве.
  - Это устройство присоединено к домену Active Directory.
  - У него должно быть прямое подключение к контроллеру домена по корпоративной проводной или беспроводной сети или через подключение удаленного доступа, например VPN-подключение.
  - Вы [развернули функцию](##step-3-roll-out-the-feature) для этого пользователя с помощью групповой политики.

Для тестирования сценария, когда пользователь вводит только имя пользователя, но не пароль.
   - Войдите в https://myapps.microsoft.com/ в новом частном сеансе браузера.

Для тестирования сценария, когда пользователю не нужно вводить имя пользователя или пароль, выполните один из следующих шагов. 
   - Войдите в https://myapps.microsoft.com/contoso.onmicrosoft.com в новом частном сеансе браузера. Введите вместо *contoso* имя своего клиента.
   - Войдите в https://myapps.microsoft.com/contoso.com в новом частном сеансе браузера. Введите вместо *contoso.com* проверенный домен (не федеративный домен) в клиенте.

## <a name="step-5-roll-over-keys"></a>Шаг 5. Смена ключей

На шаге 2 Azure AD Connect создает учетные записи компьютеров (представляющие Azure AD) во всех лесах Active Directory, для которых включен простой единый вход. Чтобы узнать больше, изучите [Подробное техническое руководство по простому единому входу Azure Active Directory](active-directory-aadconnect-sso-how-it-works.md). Для повышения безопасности рекомендуется периодически менять ключи расшифровки Kerberos для этих учетных записей компьютеров. Инструкции по смене ключей приведены в разделе [Часто задаваемые вопросы о простом едином входе Azure Active Directory](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Этот шаг не требуется выполнять _немедленно_ после включения функции. Меняйте ключи расшифровки Kerberos по крайней мере каждые 30 дней.

## <a name="next-steps"></a>Дополнительная информация

- [Подробное техническое руководство](active-directory-aadconnect-sso-how-it-works.md). Поймите, как работает функция простого единого входа.
- [Часто задаваемые вопросы](active-directory-aadconnect-sso-faq.md). Получите ответы на часто задаваемые вопросы о простом едином входе.
- [Устранение неполадок](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранять распространенные проблемы с функцией простого единого входа.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Оставить запрос на новые функции можно на форуме по Azure Active Directory.

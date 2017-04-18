---
title: "Доменные службы Azure Active Directory. Включение синхронизации паролей | Документация Майкрософт"
description: "Приступая к работе с доменными службами Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6ed35ca1b83f5f7221824d99817800c1e42e68c1
ms.lasthandoff: 04/12/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Включение синхронизации паролей с доменными службами Azure Active Directory
Выполняя предыдущие задачи, вы включили доменные службы Azure Active Directory (AD DS) для клиента Azure Active Directory (Azure AD). Следующая задача — включить необходимые хэши учетных данных, чтобы синхронизировать проверку подлинности NT LAN Manager (NTLM) и Kerberos с доменными службами Azure Active Directory. Когда синхронизация учетных данных настроена, пользователи могут входить в управляемый домен с помощью учетных данных организации.

Этапы настройки зависят от того, является ли ваша организация только облачным клиентом Azure AD или для нее настроена синхронизация локального каталога с помощью Azure AD Connect.

> [!div class="op_single_selector"]
> * [Исключительно облачный клиент Azure AD](active-directory-ds-getting-started-password-sync.md)
> * [Синхронизированный клиент Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Задача 5. Включение синхронизации паролей в доменных службах Azure Active Directory для исключительно облачного клиента Azure AD
Чтобы выполнять аутентификацию пользователей в управляемом домене, доменным службам Azure Active Directory нужны хэши учетных данных в формате, который подходит для аутентификации NTLM и Kerberos. Если не включить доменные службы Azure Active Directory для клиента, служба Azure AD не будет создавать и хранить хэши учетных данных в формате, требуемом для аутентификации NTLM или Kerberos. Из очевидных соображений безопасности служба Azure AD не хранит учетные данные в формате открытого текста. Поэтому она не может создавать хэши учетных данных NTLM и Kerberos на основании существующих учетных данных пользователей.

> [!NOTE]
> Если у вашей организации есть только облачный клиент Azure AD, пользователи, которые хотят воспользоваться доменными службами Azure Active Directory, должны изменить свои пароли.
>
>

Для этого необходимо сформировать в Azure AD хэши учетных данных, необходимые доменным службам Azure Active Directory для аутентификации Kerberos и NTLM. Вы можете либо принудительно сделать так, чтобы срок действия паролей для всех пользователей в клиенте доменных служб Azure Active Directory истек, либо отправить пользователям инструкции по изменению паролей.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Включение создания хэшей учетных данных NTLM и Kerberos для исключительно облачного клиента Azure AD
Инструкции по изменению паролей, которые необходимо отправить пользователям, приведены ниже.

1. Перейдите на корпоративную страницу с [панелью доступа Azure AD](http://myapps.microsoft.com).
2. В окне панели доступа выберите вкладку **Профиль**.
3. Щелкните плитку **Изменить пароль**.

    ![Плитка "Изменить пароль" на панели доступа Azure AD](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Если параметр **Изменить пароль** не отображается в окне панели доступа, убедитесь, что для вашей организации настроено [управление паролями в Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. На странице **изменения пароля** введите старый пароль, затем введите новый пароль и подтвердите его. 

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Щелкните **Отправить**.

Новый пароль вступает в силу в доменных службах Azure Active Directory через несколько минут после изменения. Входить на компьютеры, подключенные к управляемому домену, с помощью нового пароля можно через некоторое время (обычно это 20 минут).

## <a name="next-steps"></a>Дальнейшие действия
* [Как изменить свой пароль](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [Приступая к работе с компонентами управления паролями](../active-directory/active-directory-passwords-getting-started.md)
* [Включение синхронизации паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Администрирование управляемого домена доменных служб Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


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
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4b6da997f44860dccb2aa2571ce099ab2d0231f3
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017


---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Включение синхронизации паролей с доменными службами Azure AD
Выполняя предыдущие задачи, вы включили доменные службы Azure Active Directory для клиента Azure Active Directory (Azure AD). Следующая задача — включить синхронизацию необходимых хэшей учетных данных, чтобы проверить подлинность NTLM и Kerberos в доменных службах Azure AD. Когда синхронизация учетных данных настроена, пользователи могут входить в управляемый домен с помощью учетных данных организации.

Этапы настройки различаются для облачных учетных записей пользователей и учетных записей пользователей, синхронизированных из локального каталога с помощью Azure AD Connect.  Если клиент Azure AD содержит и пользователей в облаке, и пользователей из локального каталога AD, необходимо выполнить оба этапа.

<br>

> [!div class="op_single_selector"]
> * **Учетные записи пользователей в облаке.** [Синхронизация паролей учетных записей пользователей в облаке с управляемым доменом](active-directory-ds-getting-started-password-sync.md)
> * **Локальные учетные записи пользователей.** [Синхронизация паролей учетных записей пользователей, синхронизированных из локального каталога AD, с управляемым доменом](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Задача 5. Включение синхронизации паролей учетных записей пользователей в облаке с управляемым доменом
Чтобы выполнять аутентификацию пользователей в управляемом домене, доменным службам Azure Active Directory нужны хэши учетных данных в формате, который подходит для аутентификации NTLM и Kerberos. Пока вы не включите доменные службы Azure Active Directory для клиента, служба Azure AD не будет создавать и хранить хэши учетных данных в формате, требуемом для проверки подлинности NTLM или Kerberos. Из очевидных соображений безопасности служба Azure AD не хранит пароли в формате открытого текста. Поэтому она не может автоматически создавать хэши учетных данных NTLM и Kerberos на основании имеющихся учетных данных пользователей.

> [!NOTE]
> Если у вашей организации есть только учетные записи пользователей в облаке, пользователи, которые хотят воспользоваться доменными службами Azure Active Directory, должны изменить свои пароли. Облачная учетная запись пользователей — это учетная запись, созданная в каталоге Azure AD с помощью портала Azure или командлетов Azure AD PowerShell. Такие учетные записи пользователей не синхронизированы из локального каталога.
>
>

Для этого необходимо сформировать в Azure AD хэши учетных данных, необходимые доменным службам Azure Active Directory для аутентификации Kerberos и NTLM. Вы можете либо принудительно сделать так, чтобы срок действия паролей для всех пользователей в клиенте доменных служб Azure Active Directory истек, либо отправить пользователям инструкции по изменению паролей.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>Включение создания хэшей учетных данных NTLM и Kerberos для облачных учетных записей пользователей
Инструкции по изменению паролей, которые необходимо отправить пользователям, приведены ниже.

1. Перейдите на корпоративную страницу с [панелью доступа Azure AD](http://myapps.microsoft.com).

    ![Запуск панели доступа Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. В правом верхнем углу щелкните свое имя, а затем в открывшемся меню выберите **Профиль**.

    ![Выбор параметра "Профиль"](./media/active-directory-domain-services-getting-started/select-profile.png)

3. На странице **Профиль** щелкните **Изменение пароля**.

    ![Выбор параметра "Изменение пароля"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Если параметр **Изменить пароль** не отображается в окне панели доступа, убедитесь, что для вашей организации настроено [управление паролями в Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. На странице **изменения пароля** введите старый пароль, затем введите новый пароль и подтвердите его.

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Щелкните **Отправить**.

Новый пароль вступает в силу в доменных службах Azure Active Directory через несколько минут после изменения. Входить на компьютеры, подключенные к управляемому домену, с помощью нового пароля можно через некоторое время (обычно это 20 минут).

## <a name="related-content"></a>Похожий контент
* [Как изменить свой пароль](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Приступая к работе с компонентами управления паролями](../active-directory/active-directory-passwords-getting-started.md)
* [Включение синхронизации паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Администрирование управляемого домена доменных служб Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


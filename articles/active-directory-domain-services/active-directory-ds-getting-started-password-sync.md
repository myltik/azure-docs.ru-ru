---
title: Доменные службы Azure Active Directory. Включение синхронизации хэшей паролей | Документация Майкрософт
description: Приступая к работе с доменными службами Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/02/2018
ms.author: maheshu
ms.openlocfilehash: 4122052e48e57a27492433d01ee93536a9bbcf35
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Включение синхронизации хэшей паролей с доменными службами Azure AD
Выполняя предыдущие задачи, вы включили доменные службы Azure Active Directory для клиента Azure Active Directory (Azure AD). Следующая задача — включить синхронизацию необходимых хэшей паролей, чтобы проверить подлинность NTLM и Kerberos в доменных службах Azure AD. Когда синхронизация хэшей паролей настроена, пользователи могут входить в управляемый домен с помощью учетных данных организации.

Этапы настройки различаются для облачных учетных записей пользователей и учетных записей пользователей, синхронизированных из локального каталога с помощью Azure AD Connect. 

<br>
| **Тип учетной записи пользователя** | **Необходимые действия** |
| --- |---|
| **Учетные записи пользователей в облаке, созданные в Azure AD** |**&#x2713;** [Следуйте инструкциям в этой статье](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Учетные записи пользователей, синхронизированные из локального каталога** |**&#x2713;** [Синхронизируйте хэши паролей учетных записей пользователей, синхронизированные из локального каталога AD, с управляемым доменом](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Вам может потребоваться выполнить оба набора действий.**
> Если клиент Azure AD содержит и пользователей в облаке, и пользователей из локального каталога AD, необходимо выполнить оба набора действий.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Задача 5. Включение синхронизации хэшей паролей учетных записей пользователей в облаке с управляемым доменом
Чтобы проверять подлинность пользователей в управляемом домене, доменным службам Azure Active Directory нужны хэши паролей в формате, который подходит для проверки подлинности NTLM и Kerberos. Пока вы не включите доменные службы Azure Active Directory для клиента, служба Azure AD не будет создавать и хранить хэши паролей в формате, требуемом для проверки подлинности NTLM или Kerberos. Из очевидных соображений безопасности служба Azure AD не хранит пароли в формате открытого текста. Поэтому она не может автоматически создавать хэши паролей NTLM и Kerberos на основе существующих учетных данных пользователей.

> [!NOTE]
> **Если у вашей организации есть только учетные записи пользователей в облаке, все пользователи, которые хотят воспользоваться доменными службами Azure Active Directory, должны изменить свои пароли.** Облачная учетная запись пользователей — это учетная запись, созданная в каталоге Azure AD с помощью портала Azure или командлетов Azure AD PowerShell. Такие учетные записи пользователей не синхронизированы из локального каталога.
>
>

Для этого необходимо сформировать в Azure AD хэши паролей, необходимые доменным службам Azure Active Directory для проверки подлинности Kerberos и NTLM. Вы можете либо принудительно сделать так, чтобы срок действия паролей для всех пользователей в клиенте доменных служб Azure Active Directory истек, либо отправить пользователям инструкции по изменению паролей.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Включение создания хэшей паролей NTLM и Kerberos для облачных учетных записей пользователей
Инструкции по изменению паролей, которые необходимо отправить пользователям, приведены ниже.

1. Перейдите на корпоративную страницу с [панелью доступа Azure AD](http://myapps.microsoft.com).

    ![Запуск панели доступа Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. В правом верхнем углу щелкните свое имя, а затем в открывшемся меню выберите **Профиль**.

    ![Выбор параметра "Профиль"](./media/active-directory-domain-services-getting-started/select-profile.png)

3. На странице **Профиль** щелкните **Изменение пароля**.

    ![Выбор параметра "Изменение пароля"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Если параметр **Изменить пароль** не отображается в окне панели доступа, убедитесь, что для вашей организации настроено [управление паролями в Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. На странице **изменения пароля** введите старый пароль, затем введите новый пароль и подтвердите его.

    ![Изменения пароля пользователя](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Щелкните **Отправить**.

Новый пароль вступает в силу в доменных службах Azure Active Directory через несколько минут после изменения. Входить на компьютеры, подключенные к управляемому домену, с помощью нового пароля можно примерно через 20 минут.

## <a name="related-content"></a>Похожий контент
* [Как изменить свой пароль](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Приступая к работе с компонентами управления паролями](../active-directory/authentication/quickstart-sspr.md)
* [Включение синхронизации хэшей паролей с доменными службами Azure AD для синхронизированного клиента Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Администрирование управляемого домена доменных служб Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Присоединение виртуальной машины Red Hat Enterprise Linux 7 к управляемому домену](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

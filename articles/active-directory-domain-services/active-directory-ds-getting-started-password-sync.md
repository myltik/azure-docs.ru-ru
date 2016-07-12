<properties
	pageTitle="Доменные службы Azure AD: включение синхронизации паролей | Microsoft Azure"
	description="Приступая к работе с доменными службами Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(предварительная версия)* — включение синхронизации паролей в доменные служб Azure AD

## Задача 5. Включение синхронизации паролей в доменные службы AAD для исключительно облачного каталога Azure AD
Когда вы включите доменные службы Azure AD для своего клиента Azure AD, следующая задача — включить синхронизацию учетных данных с доменными службами Azure AD. Это позволит пользователям входить в управляемый домен, используя свои корпоративные учетные данные.

Этапы настройки отличаются в зависимости от того, есть ли у вашей организации исключительно облачный каталог Azure AD или для нее настроена синхронизация локального каталога с помощью Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Исключительно облачный каталог Azure AD](active-directory-ds-getting-started-password-sync.md)
- [Синхронизированный каталог Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Включение создания хэшей учетных данных NTLM и Kerberos для исключительно облачного каталога Azure AD
Если в вашей организации есть исключительно облачный каталог Azure AD, пользователям, которые захотят воспользоваться доменными службами Azure AD, потребуется изменить свои пароли. Для этого необходимо сформировать в Azure AD хэши учетных данных, необходимые доменным службам Azure AD для проверки подлинности Kerberos и NTLM. Вы можете либо принудительно сделать так, чтобы срок действия паролей для всех пользователей в клиенте доменных служб Azure AD истек, либо отправить пользователям инструкции по изменению паролей.

Инструкции по изменению паролей, которые необходимо отправить пользователям, таковы.

1. Перейдите на страницу панели доступа Azure AD для вашей организации. Обычно она доступна по адресу [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Выберите вкладку **Профиль** на этой странице.

3. Щелкните элемент **Изменить пароль** на этой странице, чтобы изменить пароль.

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Откроется страница **изменения пароля**. Пользователь может ввести свой текущий (старый) пароль и продолжить изменение пароля.

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

После изменения паролей новые пароли вскоре будут доступны для использования в доменных службах Azure AD. Через несколько минут пользователи смогут входить в систему компьютеров, подключенных к управляемому домену, используя новые измененные пароли.


<br>

## Похожий контент

- [Включение синхронизации паролей в доменные службы AAD для синхронизированного каталога Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)

- [Join a Windows virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-windows-vm.md) (Присоединение виртуальной машины Windows к управляемому домену доменных служб Azure AD)

- [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-rhel-linux-vm.md) (Присоединение виртуальной машины Red Hat Enterprise Linux к управляемому домену доменных служб Azure AD)

<!---HONumber=AcomDC_0706_2016-->
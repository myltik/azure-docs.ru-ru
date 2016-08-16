<properties 
	pageTitle="Начало работы с поставщиком Microsoft Azure Multi Factor Authentication" 
	description="Из этой статьи вы узнаете, как создать поставщик Azure Multi Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>



# Начало работы с поставщиком Azure Multi Factor Authentication
Многофакторная проверка подлинности доступна по умолчанию для глобальных администраторов, у которых есть служба Azure Active Directory, и для пользователей Office 365. Однако если вы хотите использовать преимущества [дополнительных функций](multi-factor-authentication-whats-next.md), необходимо приобрести полную версию Azure MFA.

> [AZURE.NOTE]  Поставщик Azure MFA используется для получения всех преимуществ полной версии Azure MFA. Он предназначен для пользователей, у которых **нет лицензий Azure MFA, Azure AD Premium или Enterprise Mobility Suite**. Эти решения по умолчанию включают в себя полную версию Azure MFA. Если у вас есть лицензии, вам не нужно создавать поставщик Azure Multi Factor Authentication.

Поставщик Azure Multi Factor Authentication требуется, если вы хотите загрузить пакет SDK.

> [AZURE.IMPORTANT]  Если вы хотите загрузить пакет SDK, необходимо создать поставщик Azure Multi Factor Authentication, даже если у вас уже есть лицензии Azure MFA, Azure AD Premium или EMS. Если вы создаете поставщик Azure Multi Factor Authentication для этой цели и у вас уже есть лицензии, поставщик нужно создавать с моделью лицензий **для каждого пользователя**, а затем привязать его к каталогу, который содержит лицензии Azure MFA, Azure AD Premium или EMS. В таком случае с вас не будет взиматься плата, пока количество уникальных пользователей пакета SDK не превысит количество лицензий, которыми вы владеете.
 
Ниже показано, как создать поставщик Azure Multi Factor Authentication.

## Создание поставщика Azure Multi Factor Authentication
--------------------------------------------------------------------------------

1. Войдите на **классический портал Azure** с учетной записью администратора.
2. Выберите **Active Directory** слева.
3. На странице Active Directory вверху откройте вкладку **Поставщики Azure Multi Factor Authentication**. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. В нижней части страницы нажмите кнопку **Создать**. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. В разделе **Службы приложений** выберите элемент **Поставщик Multi Factor Authentication**. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Выберите **Быстрое создание**. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Заполните перечисленные ниже поля и нажмите кнопку **Создать**.
	1. **Имя** — имя поставщика Multi-Factor Authentication.
	2. **Модель использования** — модель использования поставщика Multi-Factor Authentication.
		- "На проверку подлинности" — покупательская модель, в которой оплачивается каждая проверка подлинности. Обычно используется в сценариях, использующих Azure Multi-Factor Authentication в потребительском приложении.
		- "На включенного пользователя" — покупательская модель, в которой оплачивается каждый включенный пользователь. Обычно используется для доступа сотрудников к таким приложениям, как Office 365.
	2. **Каталог** — клиент Azure Active Directory, с которым связан поставщик Multi-Factor Authentication. Обратите внимание на такие моменты:
		- Каталог Azure AD не требуется для создания поставщика Multi-Factor Authentication. Если вы планируете использовать только сервер Azure Multi-Factor Authentication или пакет SDK, это поле можно оставить пустым.
		- Чтобы пользоваться преимуществами расширенных функций, необходимо связать поставщик Azure MFA с каталогом Azure AD.
		- Средства синхронизации Azure AD Connect, AAD Sync или DirSync требуются только при синхронизации локальной среды Active Directory с каталогом Azure AD. Если вы используете только один несинхронизированный каталог Azure AD, синхронизация не требуется. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. После нажатия кнопки "Создать" будет создан поставщик Multi-Factor Authentication и появится сообщение **Поставщик Multi-Factor Authentication успешно создан**. Нажмите кнопку **ОК**. ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0810_2016-->
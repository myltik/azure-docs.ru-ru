<properties 
	pageTitle="Приступая к работе с Azure Multi-Factor Authentication в облаке" 
	description="Эта страница посвящена службе Azure Multi-Factor Authentication. Она содержит сведения по началу работы с Azure Multi-Factor Authentication в облаке." 
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
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Приступая к работе с Azure Multi-Factor Authentication в облаке



<center>![MFA in the Cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

Теперь, определив, что многофакторную проверку подлинности мы будем использовать в облаке, давайте перейдем к делу. Чтобы начать работу с Azure Multi-Factor Authentication не нужно много усилий. Обратите внимание, что при использовании Multi-Factor Authentication для Office 365 или Multi-Factor Authentication для администраторов Azure можно перейти к шагу 3.


1. [Зарегистрируйтесь, чтобы оформить подписку Azure.](https://azure.microsoft.com/pricing/free-trial/)
	- Если у вас еще нет подписки Azure, вам необходимо зарегистрироваться для ее оформления. Если вы только начинаете работать с Azure Multi-Factor Authentication (MFA), используйте пробную подписку.
2. [Создайте поставщик Multi-Factor Authentication](#creating-an-azure-multi-factor-auth-provider) или [назначьте пользователям лицензии](#assigning-an-azure-ad-premium-or-enterprise-mobility-license-to-users).
	- Далее нужно сделать следующее. Создайте поставщик Azure Multi-Factor Authentication и назначьте его своему каталогу либо назначьте пользователям лицензии. Лицензии доступны пользователям, у которых есть Azure MFA, Azure AD Premium или Enterprise Mobility Suite. Служба Azure Multi-Factor Authentication является частью Azure Active Directory Premium. Она также включена в Enterprise Mobility Suite. Если у вас достаточно лицензий на использование многофакторной проверки подлинности для всех пользователей, создавать поставщик не нужно. 
3. [Включите Multi-Factor Authentication для пользователей.](#turn-on-multi-factor-authentication-for-users)
	- Включите Azure Multi-Factor Authentication для пользователей через портал Office 365 или Azure. 
4. [Отправьте пользователям сообщение электронной почты, чтобы уведомить их о включении Multi-Factor Authentication.](#send-email-to-end-users)
	- После включения многофакторной проверки подлинности в учетных записях пользователей рекомендуется уведомить их об этом по электронной почте. При следующем входе в учетную запись пользователям будет предложено завершить процесс, так что благодаря этому сообщению они будут знать, чего ожидать. 



## Создание поставщика Azure Multi Factor Authentication
Многофакторная проверка подлинности доступна по умолчанию для глобальных администраторов, у которых есть служба Azure Active Directory, и для пользователей Office 365. Однако если вы хотите использовать преимущества дополнительных функций, необходимо приобрести полную версию Azure MFA.

Поставщик Azure MFA используется для получения всех преимуществ полной версии Azure MFA. Он предназначен для пользователей, у которых нет лицензий Azure MFA, Azure AD Premium или Enterprise Mobility Suite. Эти решения по умолчанию включают в себя полную версию Azure MFA. Если у вас есть лицензии, не нужно создавать поставщик Azure MFA. Следующие шаги демонстрируют, как создать этот поставщик.

### Создание поставщика Azure Multi Factor Authentication
--------------------------------------------------------------------------------

1. Войдите на портал Azure с учетной записью администратора.
2. Выберите слева элемент Active Directory.
3. На странице Active Directory вверху выберите вкладку "Поставщики Azure Multi Factor Authentication". В нижней части страницы нажмите кнопку **Создать**.
4. В разделе "Службы приложений" выберите элемент "Поставщики многофакторной проверки подлинности", а затем — "Быстрое создание".
5. Заполните перечисленные ниже поля и нажмите кнопку "Создать".
	1. "Имя" — имя поставщика Multi-Factor Authentication.
	2. "Модель использования" — модель использования поставщика Multi-Factor Authentication.
		- "На проверку подлинности" — покупательская модель, в которой оплачивается каждая проверка подлинности. Обычно используется в сценариях, использующих Azure Multi-Factor Authentication в потребительском приложении.
		- "На включенного пользователя" — покупательская модель, в которой оплачивается каждый включенный пользователь. Обычно используется для доступа сотрудников к таким приложениям, как Office 365.
	2. "Каталог" — клиент Azure Active Directory, с которым связан поставщик Multi-Factor Authentication. Обратите внимание на такие моменты:
		- Каталог Azure AD не требуется для создания поставщика Multi-Factor Authentication. Если вы планируете использовать только сервер Azure Multi-Factor Authentication или пакет SDK, это поле можно оставить пустым.
		- Чтобы пользоваться преимуществами расширенных функций, вам необходимо связать поставщик Azure MFA с каталогом Azure AD.
		- Средства синхронизации Azure AD Connect, AAD Sync или DirSync требуются только при синхронизации локальной среды Active Directory с каталогом Azure AD. Если вы используете только один не синхронизированный каталог Azure AD, синхронизация не требуется.
		



5. После нажатия кнопки "Создать" будет создан поставщик Multi-Factor Authentication и появится сообщение "Поставщик Multi-Factor Authentication успешно создан". Нажмите кнопку "ОК".

![Создание поставщика MFA](./media/multi-factor-authentication-get-started-cloud/provider.png)
	 
## Назначение пользователям лицензии Azure MFA, Azure AD Premium или Enterprise Mobility

Если вы приобрели лицензии Azure MFA, Azure AD Premium или Enterprise Mobility Suite, поставщик Multi-Factor Authentication создавать не нужно. Необходимо просто назначить эти лицензии пользователям, после чего для них можно включить MFA.

### Назначение лицензии Azure MFA, Azure AD Premium или Enterprise Mobility Suite
--------------------------------------------------------------------------------

1. Войдите на портал Azure с учетной записью администратора.
2. Выберите **Active Directory** слева.
3. На странице "Active Directory" дважды щелкните каталог пользователей, которых вы хотите активировать.
4. В верхней части страницы каталога выберите **Лицензии**.
5. На странице "Лицензии" выберите Azure Multi-Factor Authentication, Active Directory Premium или Enterprise Mobility Suite, а затем щелкните "Назначить". ![Назначение лицензий](./media/multi-factor-authentication-get-started-cloud/license2.png)
6. В диалоговом окне выберите пользователей или группы, которым требуется назначить лицензии, и щелкните значок галочки, чтобы сохранить изменения.






## Включение многофакторной проверки подлинности для пользователей

Учетные записи пользователей в службе Azure Multi-Factor Authentication имеют три различных состояния:

Состояние | Описание |Затронутые приложения, не использующие браузер| Примечания 
:-------------: | :-------------: |:-------------: |:-------------: |
Отключено | Состояние по умолчанию для нового пользователя, не зарегистрированного в многофакторной проверке подлинности.|Нет|Пользователь не использует многофакторную проверку подлинности.
Включено |Пользователь зарегистрирован в многофакторной проверке подлинности.|Нет. Они будут продолжать работать, пока не завершится регистрация.|Пользователь включен, но не завершил процесс регистрации. Ему будет предложено завершить процесс при следующем входе в систему.
Принудительно|Пользователь зарегистрирован и завершил процесс регистрации для использования многофакторной проверки подлинности.|Да. Они не будут работать, пока не будут созданы и использованы пароли приложений. | Пользователь завершил или не завершил процесс регистрации. Если пользователь завершил регистрацию, он будет использовать многофакторную проверку подлинности. Если нет, пользователю будет предложено завершить процесс при следующем входе в систему.

Чтобы включить MFA для пользователей, выполните процедуру ниже.

### Включение многофакторной проверки подлинности
--------------------------------------------------------------------------------
1.  Войдите на портал управления Azure с учетной записью администратора.
2.  Слева щелкните элемент Active Directory.
3.  В разделе "Каталог" выберите каталог пользователя, которого вы хотите включить.
4.  В верхней части щелкните "Пользователи".
5.  В нижней части страницы щелкните "Управление Multi-Factor Authentication".
6.  Найдите пользователя, для которого нужно включить многофакторную проверку подлинности. Возможно, потребуется изменить представление в верхней части страницы. Убедитесь, что у пользователя состояние "Отключен", и установите флажок рядом с его именем.
7.  В правой части окна щелкните "Включить". Щелкните "Включить многофакторную проверку подлинности".
8.  После включения рекомендуется уведомить пользователей об этом. Это можно сделать по электронной почте. В сообщении также следует рассказать, как использовать приложения, не использующие браузер, чтобы пользователя не заблокировали.

<center>![Enable Users](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

Чтобы изменить состояние с помощью Windows PowerShell, можно использовать команды ниже. Для параметра `$st.State` можно задать одно из упомянутых состояний.

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## Отправка электронных сообщений пользователям

После активации пользователей рекомендуется отправить им электронное сообщение о том, что им необходимо указать контактную информацию. Ниже приведен шаблон сообщения электронной почты, который вы можете использовать. В нем есть ссылка на видео для пользователей.

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application,   and sign-in with the username and app password. Follow the steps documented : http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## Дальнейшие действия
Теперь, когда многофакторная проверка подлинности в облаке настроена, можно настроить и выполнить развертывание. См. статью [Настройка Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).

<!---HONumber=AcomDC_0323_2016-->

<properties
	pageTitle="Вход в мобильное приложение с помощью кода подтверждения службы Azure Multi-Factor Authentication"
	description="На этой странице описана процедура входа пользователей с помощью кода подтверждения мобильного приложения при использовании службы Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Вход в мобильное приложение с помощью кода подтверждения службы Azure Multi-Factor Authentication


Ниже описана процедура использования многофакторной проверки подлинности для входа в мобильное приложение с помощью кода подтверждения.

## Вход в мобильное приложение с использованием кода подтверждения

<ol>

<li>Войдите в приложение или службу, например Office&#160;365, используя имя пользователя и пароль.</li>
<li>Вам будет предложено ввести код подтверждения.</li>


<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)</center>

<li>Откройте приложение Azure Authenticatior на телефоне и введите код в поле для входа.</li>

<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)</center>


<li>После этого вы войдете в систему.</li>

<!---HONumber=AcomDC_0921_2016-->
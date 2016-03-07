<properties
	pageTitle="Настройка устройства с Windows 10 для работы с Azure AD с помощью меню «Параметры» | Microsoft Azure"
	description="Сведения о том, как пользователи могут присоединиться к Azure AD, используя меню ";Параметры";."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="femila"/>

# Настройка устройства с Windows 10 для работы с Azure AD с помощью меню «Параметры»
Если вы уже используете Windows 7 или Windows 8, и система на вашем компьютере или устройстве была обновлена до Windows 10, его можно присоединить к Azure Active Directory (Azure AD) через меню "Параметры".

## Присоединение к Azure AD с помощью меню "Параметры"


1. В меню **Пуск** выберите пункт **Параметры**.
2. В разделе **Параметры** выберите **Система** -> **О системе** -> **Присоединение к Azure AD**.
<center> ![Присоединение к Azure AD из меню "Параметры"](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. В окне сообщения о присоединении к Azure AD нажмите кнопку **Продолжить**.
<center> ![Окно сообщения о присоединении к Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Введите учетные данные для входа. На этом этапе вы выполните все действия, необходимые для прохождения аутентификации. Если вы входите в федеративный клиент, администратор вашей организации обеспечит возможность федеративного входа.
<center> ![Ввод учетных данных для входа](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Если для присоединения к Azure AD в организации настроена Azure Multi-Factor Authentication, перед продолжением введите данные для второй части проверки.
6. На экране с запросом **Разрешить управление этим устройством?** нажмите кнопку **Принимаю**.
7. Вы увидите сообщение "Теперь устройство присоединено к вашей организации в Azure AD".


## Дополнительная информация
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)
* [Проверка подлинности удостоверений без использования паролей с помощью службы Microsoft Passport](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0224_2016-->
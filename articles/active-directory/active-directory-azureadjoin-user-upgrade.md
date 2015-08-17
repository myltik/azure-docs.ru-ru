<properties 
	pageTitle="Настройка устройства с Windows 10 для работы с Azure AD с помощью меню «Параметры» | Microsoft Azure" 
	description="В этом разделе рассматривается, как пользователи могут присоединиться к Azure AD, используя меню «Параметры»." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/02/2015" 
	ms.author="femila"/>

# Настройка устройства с Windows 10 для работы с Azure AD с помощью меню «Параметры»
Если вы обновили свою операционную систему Windows 7 или 8 до Windows 10, то можете присоединиться к Azure AD, используя меню «Параметры».

Присоединение к Azure AD с помощью меню «Параметры»
-----------------------------------------------------------------------------------------------

1. В главном меню выберите пункт «Параметры».
2. Выберите Параметры->**Система**->**О системе**->**Присоединение к Azure AD**.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. В окне с сообщением о присоединении к Azure AD нажмите кнопку **Продолжить**.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Введите учетные данные для входа. На этом этапе вы выполните все действия, необходимые для проверки подлинности. Если вы входите в федеративный клиент, ваш администратор обеспечит возможность федеративного входа.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Если для присоединения к Azure AD в организации настроена многофакторная проверка подлинности, перед продолжением потребуется выполнить вторую часть проверки.
6. На экране с запросом «Разрешить управление этим устройством?» нажмите кнопку **Принимаю**.
7. Вы увидите сообщение «Теперь устройство присоединено к вашей организации в Azure AD».


## Дополнительная информация
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования и рекомендации по развертыванию для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md).

<!---HONumber=August15_HO6-->


<properties
	pageTitle="Присоединение личного устройства к своей организации | Microsoft Azure"
	description="Поясняет, как пользователи могут регистрировать свои персональные устройства Windows 10 в корпоративной сети, а также шаги по развертыванию для сценария BYOD."
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
	ms.date="02/26/2016"
	ms.author="femila"/>

# Присоединение личного устройства к своей организации

## Присоединение личного устройства с Windows 10 к своей организации

1.	В меню **Пуск** выберите **Параметры**.
2.	Выберите пункт **Учетные записи**, а затем нажмите кнопку **Ваша учетная запись**.
3.	Выберите команду **Добавить рабочую или учебную учетную запись** и введите свою корпоративную учетную запись.
4.	На странице входа своей организации введите имя пользователя и пароль, затем нажмите кнопку **ОК**.
5.	Появится окно многофакторной проверки подлинности. (Этот запрос может настроить ИТ-администратор).
6.	Azure Active Directory (Azure AD) проверяет, требует ли устройство регистрации в службе управления мобильными устройствами.
7.	Windows регистрирует устройство в каталоге организации в Azure AD и в службе управления мобильными устройствами, если это уместно.
8.	Если вы являетесь управляемым пользователем, Windows осуществляет переход на рабочий стол с помощью автоматического входа.
9.	Если вы являетесь федеративным пользователем, то появится экран входа в Windows, где нужно будет ввести учетные данные.

## Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Проверка подлинности удостоверений без использования паролей с помощью службы Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->
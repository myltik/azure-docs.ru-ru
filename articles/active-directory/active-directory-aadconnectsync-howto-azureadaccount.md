<properties
	pageTitle="Синхронизация Azure AD Connect: управление учетной записью службы Azure AD | Microsoft Azure"
	description="В этой статье описывается процедура восстановления учетной записи службы Azure AD."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054 Как сбросить пароль для учетной записи службы соединителя синхронизации Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="andkjell"/>

# Синхронизация Azure AD Connect: управление учетной записью службы Azure AD
Учетная запись службы, используемая соединителем Azure AD, как правило, не нуждается в обслуживании. Если требуется сбросить ее учетные данные, в этой статье вы найдете необходимые сведения. Например, это потребуется, если глобальный администратор по ошибке сбросит пароль учетной записи службы с помощью PowerShell.

## Сброс учетных данных
Если учетная запись службы, определенная в соединителе Azure AD, не может связаться с Azure AD из-за проблем с проверкой подлинности, можно сбросить пароль.

1. Войдите на сервер синхронизации Azure AD Connect и запустите PowerShell.
2. Запустите `Add-ADSyncAADServiceAccount`. ![Командлет PowerShell addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Введите учетные данные глобального администратора Azure AD.

Этот командлет сбрасывает пароль для учетной записи службы и обновляет его в Azure AD и в модуле синхронизации.

## Известные проблемы, которые можно решить с помощью описанных действий
Ниже представлен список обнаруженных пользователями ошибок, которые можно исправить путем сброса учетных данных учетной записи службы Azure AD.

-----------
Событие 6900 Сервер обнаружил непредвиденную ошибку при обработке уведомления об изменении пароля. AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: старый пароль используется для проверки подлинности.

----------
Событие 659 Произошла ошибка при получении конфигурации синхронизации политики паролей. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException. AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: старый пароль используется для проверки подлинности.

## Дальнейшие действия

**Обзорные статьи**

- [Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка](active-directory-aadconnectsync-whatis.md)
- [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->
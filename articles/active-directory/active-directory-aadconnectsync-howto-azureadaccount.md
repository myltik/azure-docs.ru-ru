<properties
	pageTitle="Синхронизация Azure AD Connect: управление учетной записью службы Azure AD | Microsoft Azure"
	description="В этом разделе описывается процедура восстановления учетной записи службы Azure AD."
	services="active-directory"
    keywords="Сброс пароля для учетной записи службы соединителя синхронизации Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Синхронизация Azure AD Connect: управление учетной записью службы Azure AD
Учетная запись службы, используемая соединителем Azure AD, как правило, не нуждается в обслуживании. Но если требуется сбросить ее учетные данные, то в этом разделе вы найдете необходимые сведения. Например, это может произойти, если глобальный администратор по ошибке сбросит пароль учетной записи службы с помощью PowerShell.

## Сброс учетных данных
Если учетная запись службы, определенная в соединителе Azure AD, не может связаться с Azure AD из-за проблем с проверкой подлинности, можно сбросить пароль.

1. Войдите на сервер синхронизации Azure AD Connect и запустите PowerShell.
2. Запустите `Add-ADSyncAADServiceAccount`. ![Командлет PowerShell addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Введите учетные данные глобального администратора Azure AD.

Этот командлет сбросит пароль для учетной записи службы и обновит его в Azure AD и модуле синхронизации.

## Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0615_2016-->
<properties
   pageTitle="Устранение неполадок: элемент Active Directory отсутствует или недоступен | Microsoft Azure "
   description="Что делать, если пункт меню Active Directory не отображается на портале управления Azure."
   services="active-directory"
   documentationCenter="na"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# Устранение неполадок: элемент Active Directory отсутствует или недоступен

Многие указания по использованию функций и служб Azure Active Directory начинаются с «Перейдите на портал управления Azure и щелкните **Active Directory**». Но что делать, если расширение или пункт меню Active Directory не отображается или помечен **Недоступно**? В этой статье вы найдете нужные ответы. В ней описываются условия, при которых элемент **Active Directory** не отображается или недоступен, и дальнейшие действия в такой ситуации.

## Active Directory отсутствует

Как правило, элемент **Active Directory** отображается в левом меню навигации. В указаниях процедур Azure Active Directory предполагается, что этот элемент отображается в представлении.

![Снимок экрана: Active Directory в Azure](./media/active-directory-troubleshooting/typical-view.png)

Элемент Active Directory отображается в левом меню навигации при выполнении любого из следующих условий. В противном случае он не отображается.

* Текущий пользователь вошел в систему через учетную запись Майкрософт (ранее известную как Windows Live ID).

    ИЛИ

* У клиента Azure есть каталог, а владелец текущей учетной записи является администратором каталога.

    ИЛИ

* У клиента Azure есть хотя бы одно пространство имен контроля доступа Azure AD (ACS). Дополнительную информацию см. в разделе [Пространство имен Access Control](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    ИЛИ

* У клиента Azure есть хотя бы один поставщик Azure Multi-Factor Authentication. Дополнительную информацию см. в разделе [Управление поставщиками Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Чтобы создать пространство имен контроля доступа или поставщик Multi-Factor Authentication, щелкните **+Создать** > **Службы приложений** > **Active Directory**.

Чтобы получить права администратора для каталога, обратитесь к администратору, чтобы он назначил вашей учетной записи роль администратора. Дополнительную информацию см. в статье [Назначение ролей администратора](active-directory-assign-admin-roles.md).

## Active Directory недоступен

Элемент **Active Directory** отобразится, если щелкнуть **+Создать** > **Службы приложений**. В частности, элемент Active Directory появляется, когда любая из функций Active Directory, такая как каталог, управление доступом или поставщик Multi-Factor Auth, доступна текущему пользователю.

Однако при загрузке страницы элемент будет недоступен и помечен **Недоступно**. Это временное состояние. Если подождать несколько секунд, элемент станет доступным. Если задержка длится дольше, зачастую достаточно просто обновить веб-страницу.

![Снимок экрана: Active Directory недоступен](./media/active-directory-troubleshooting/not-available.png)

<!---HONumber=AcomDC_0921_2016-->
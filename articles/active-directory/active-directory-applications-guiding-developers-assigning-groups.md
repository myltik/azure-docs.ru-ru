<properties
	pageTitle="Azure AD и приложения: назначение групп для приложения | Microsoft Azure"
	description="Как реализовать назначение групп для приложений Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="inhenk"/>

# Azure AD и приложения: назначение групп для приложения
Прежде чем назначить пользователей и группы для приложения, необходимо настроить требование назначения пользователей. Чтобы узнать, как настроить требование назначения пользователей, см. статью [Требование назначения пользователей](active-directory-applications-guiding-developers-requiring-user-assignment.md).

В этой статье предполагается, что вы уже создали в Active Directory группу, которая используется для этого приложения.

## Назначение групп для приложения
1. Войдите на портал Azure с учетной записью администратора.
2. В главном меню выберите пункт **Все элементы**.
3. Выберите каталог, который используете для приложения.
4. Откройте вкладку **ПРИЛОЖЕНИЯ**.
5. Выберите приложение из списка приложений, связанных с данным каталогом.
6. Откройте вкладку **ПОЛЬЗОВАТЕЛИ И ГРУППЫ**.
7. Отфильтруйте список групп в Active Directory с помощью раскрывающегося списка **Группы**.
8. Выберите группу.
9. Щелкните **НАЗНАЧИТЬ**.
10. Щелкните **Да** при появлении запроса.

## Дальнейшие действия
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_0928_2016-->
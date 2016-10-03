<properties
   pageTitle="Azure Active Directory сообщает о событии ";Неизвестный субъект"; | Microsoft Azure"
   description="Описание события ";Неизвестный субъект"; в отчетах Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="SSalahAhmed"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="saah"/>

# Azure Active Directory сообщает о событии "Неизвестный субъект"

*Данная документация является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*

В редких случаях вы можете увидеть необычные значения в полях "Субъект" или "Пользователь" в отчетах Azure AD. Такое поведение является нормальным и вызывается одним из двух следующих событий:

## Субъект-служба работает с каталогом без контекста пользователя

В этом случае субъект-служба (приложение) выполняет обновления каталога без входа в систему от имени пользователя. Это приводит к тому, что идентификатор субъекта-службы отображается как субъект вместо имени участника-пользователя. Ниже приведен пример:

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Это известная ошибка, и мы работаем над ее устранением.

## Пользователь был удален из каталога перед обработкой события

В этом случае пользователь был удален из каталога, прежде чем мы обработали событие и связали с ним имя пользователя. Ниже приведен пример:

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Это известная ошибка, и мы работаем над ее устранением.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_0921_2016-->
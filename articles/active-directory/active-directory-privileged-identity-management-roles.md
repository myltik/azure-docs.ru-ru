<properties
   pageTitle="Роли в PIM | Microsoft Azure"
   description="Узнайте, какие роли можно использовать для привилегированных пользователей с помощью расширения для управления привилегированными пользователями Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Управление привилегированными пользователями Azure AD: роли

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Роли из Azure Active Directory, Office 365 и других источников

Система управления привилегированными пользователями Azure (PIM) использует указанные ниже роли в качестве ролей администраторов по умолчанию.

- глобального администратора;
- администратора выставления счетов;
- администратора служб;
- администратора пользователей;
- администратора паролей.

Дополнительные сведения о ролях Office 365, Exchange Online, Sharepoint Online и Skype для бизнеса см. в статье [Назначение ролей администратора в Office 365](https://support.office.com/ru-RU/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=ru-RU&rs=ru-RU&ad=US).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Роли пользователей и вход в систему
> [AZURE.NOTE]Чтобы пользователь мог входить в систему управления привилегированными пользователями Azure, у него должна быть лицензия на использование Azure.

## Назначение лицензии пользователю в Azure AD

> [AZURE.NOTE] Параметр лицензии отображается только тогда, когда для этой подписки на самом деле имеются лицензии.

1. Войдите в систему по ссылке [http://manage.windowsazure.com](http://manage.windowsazure.com) с помощью учетной записи глобального администратора или соадминистратора.
2. В главном меню выберите пункт **Все элементы**.
3. Выберите каталог, с которым необходимо работать и с которым сопоставлены соответствующие лицензии.
4. Щелкните **Лицензии**. Отобразится список доступных лицензий.
5. Щелкните план лицензирования, содержащий лицензии, которые необходимо распространить.
6. Щелкните **Назначить пользователей**.
7. Выберите пользователя, которому необходимо назначить лицензию.
8. Нажмите кнопку **Назначить**. Теперь этот пользователь может войти в Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->
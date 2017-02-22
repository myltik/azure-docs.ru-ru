---
title: "Устранение неполадок службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Способы устранения распространенных проблем со службой совместной работы Azure Active Directory B2B."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Устранение неполадок службы совместной работы Azure Active Directory B2B

В этой статье описаны способы устранения распространенных проблем, возникающих в службе совместной работы Azure Active Directory (Azure AD) B2B.

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>Не удается создать внешнего пользователя из-за существующего контакта

Если для приглашаемого внешнего пользователя уже существует контактный объект, то вы не сможете пригласить этого пользователя до устранения конфликта. Как правило, это устраняется удалением контактного объекта. Пока не вышла общедоступная версия службы совместной работы B2B, этот конфликт необходимо устранить вручную.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>После добавления внешнего пользователя он не отображается в глобальной адресной книге или в средстве выбора пользователей

В тех случаях, когда данные внешних пользователей не заполняются в списке, репликация объекта может занять несколько минут.

## <a name="invitations-have-been-disabled-for-directory"></a>Приглашения отключены для каталога

Если появляется сообщение об ошибке, указывающее, что у вас нет разрешений на приглашение пользователей, то проверьте, есть ли у вашей учетной записи полномочия на приглашение внешних пользователей. Это можно проверить в параметрах пользователей:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Если вы недавно изменили эти параметры или назначили пользователю роль лица, приглашающего гостей, то изменения вступят в силу примерно через 15–60 минут.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Пользователь, которого я пригласил, в процессе активации получает сообщение об ошибке

Ниже перечислены распространенные ошибки.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Администратор приглашенного пользователя запретил создавать в клиенте пользователей с атрибутом EmailVerified

Если приглашаются пользователи из организации, в которой используется каталог Azure Active Directory, где отсутствует учетная запись определенного пользователя (пользователь не существует в каталоге AAD contoso.com). Администратор contoso.com мог настроить политику, которая запрещает создавать пользователей. Внешний пользователь должен обратиться к своему администратору и убедиться, что создание внешних пользователей разрешено. Администратору может потребоваться разрешить создание в своем домене пользователей, выполнивших проверку адреса электронной почты (сведения о разрешении пользователей с атрибутом EmailVerified см. в [этой статье](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters)).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Внешний пользователь уже не существует в федеративном домене

В случаях, когда внешний пользователь использует решение федерации, где аутентификация выполняется локально, а пользователь уже не существует в Azure Active Directory, пригласить пользователя невозможно.

Для устранения этой проблемы администратор внешнего пользователя должен синхронизировать учетную запись этого пользователя с Azure Active Directory.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>Как знак "\#", который обычно является недопустимым, синхронизируется с Azure AD?

"\#" — это зарезервированный знак в именах участников-пользователей для службы совместной работы Azure AD B2B или внешних пользователей (приглашенный пользователь &lt;user@contoso.com&gt; становится &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;). Поэтому при входе на портал Azure не разрешается использовать знак \# в именах участников-пользователей из локальной среды.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>При добавлении внешних пользователей в синхронизированную группу появляется сообщение об ошибке

Внешних пользователей можно добавлять только в "назначенные" группы или группы "безопасности", а не в локально управляемые группы.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Внешний пользователь не получил оправленное ему сообщение для активации

Приглашенному пользователю следует обратиться к своему поставщику услуг Интернета или проверить настройки фильтра нежелательной почты и убедиться, что этот адрес разрешен: &lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>Получатель получил несколько сообщений с приглашением

В некоторых случаях, когда у получателя приглашения есть несколько псевдонимов для учетной записи, он может получить два приглашения. В таких случаях учетная запись создается для первой активированной ссылки, а вторая ссылка на активацию становится недействительной.

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->



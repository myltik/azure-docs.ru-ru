---
title: Устранение неполадок службы совместной работы Azure Active Directory B2B | Документация Майкрософт
description: Способы устранения распространенных проблем со службой совместной работы Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 82c171c59b7f465afd760d50b36d17912ee7c8e3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Устранение неполадок службы совместной работы Azure Active Directory B2B

В этой статье описаны способы устранения распространенных проблем, возникающих в службе совместной работы Azure Active Directory (Azure AD) B2B.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>После добавления внешнего пользователя он не отображается в глобальной адресной книге или в средстве выбора пользователей

В тех случаях, когда данные внешних пользователей не заполняются в списке, репликация объекта может занять несколько минут.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Гостевой пользователь B2B не отображается в средстве выбора людей SharePoint Online или OneDrive 
 
Возможность поиска существующих гостевых пользователей в средстве выбора пользователей SharePoint Online (SPO) отключена по умолчанию для согласования с поведением предыдущих версий.

Вы можете включить эту функцию, используя параметр ShowPeoplePickerSuggestionsForGuestUsers на уровне семейства веб-сайтов и коллекции клиентов. Задать параметр этой функции можно с помощью командлетов SPOTenant и SPOSite, которые позволяют участникам искать всех существующих гостевых пользователей в каталоге. Изменения в области клиента не влияют на уже подготовленные сайты SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Приглашения отключены для каталога

Если появляется уведомление, указывающее, что у вас нет разрешений на приглашение пользователей, проверьте в разделе "Параметры пользователей", есть ли у вашей учетной записи полномочия на приглашение внешних пользователей.

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Если вы недавно изменили эти параметры или назначили пользователю роль лица, приглашающего гостей, то изменения вступят в силу примерно через 15–60 минут.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Пользователь, которого я пригласил, в процессе активации получает сообщение об ошибке

Ниже перечислены распространенные ошибки.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Администратор приглашенного пользователя запретил создавать в клиенте пользователей с атрибутом EmailVerified

Если приглашаются пользователи из организации, в которой используется каталог Azure Active Directory, где отсутствует учетная запись определенного пользователя (например, пользователь не существует в каталоге AAD contoso.com). Администратор contoso.com мог настроить политику, которая запрещает создавать пользователей. Пользователь должен уточнить у администратора, разрешается ли приглашать внешних пользователей. Администратору внешнего пользователя может потребоваться разрешить в своем домене пользователей с проверенным адресом электронной почты (сведения о разрешении пользователей с проверенным адресом электронной почты см. в этой [статье](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Внешний пользователь уже не существует в федеративном домене

Если используется проверка подлинности федерации, и пользователь уже не существует в Azure Active Directory, пригласить его невозможно.

Для устранения этой проблемы администратор внешнего пользователя должен синхронизировать учетную запись этого пользователя с Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Как символ "\#", который обычно является недопустимым, синхронизируется с Azure AD?

Знак "\#" зарезервирован для имен участников-пользователей службы совместной работы Azure AD B2B или внешних пользователей (имя учетной записи приглашенного пользователя user@contoso.com преобразуется в user_contoso.com#EXT#@fabrikam.onmicrosoft.com). Поэтому при входе на портал Azure не разрешается использовать знак "\#" в именах участников-пользователей из локальной среды. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>При добавлении внешних пользователей в синхронизированную группу появляется сообщение об ошибке

Внешних пользователей можно добавлять только в "назначенные" группы или группы "безопасности", а не в локально управляемые группы.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Внешний пользователь не получил оправленное ему сообщение для активации

Приглашенному пользователю следует обратиться к своему поставщику услуг Интернета или проверить настройки фильтра нежелательной почты и убедиться, что этот адрес разрешен: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Пользовательское сообщение иногда не включаются в сообщение с приглашением

Для соблюдения требований законов о защите конфиденциальности данных наши API-интерфейсы не включают пользовательские сообщения в текст сообщений с приглашением:

- если в приглашающем клиенте не указан адрес электронной почты приглашающего;
- если приглашение отправляет субъект-служба приложения.

Если вам важно использовать именно этот сценарий, вы можете запретить отправку сообщения с приглашением из API-интерфейса, и отправить его через любой другой механизм отправки электронной почты. Проконсультируйтесь к юристом своей организации, чтобы убедиться, что отправляемые таким образом сообщения не нарушают требования законов о защите конфиденциальности данных.

## <a name="next-steps"></a>Дополнительная информация

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

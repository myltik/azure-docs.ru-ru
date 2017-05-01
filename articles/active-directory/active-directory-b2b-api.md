---
title: "API службы совместной работы Azure Active Directory B2B и настройка | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: c85e05b38b4a9525e13ec510a17b7ef4841198d7
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API службы совместной работы Azure Active Directory B2B и настройка

Очень многие клиенты высказали пожелание, чтобы процесс приглашения был более индивидуальным и соответствовал потребностям конкретной организации. С помощью нашего интерфейса API это можно сделать. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Возможности API приглашения
Интерфейс API предоставляет следующие возможности:

1. Пригласите внешнего пользователя, используя *любой* адрес электронной почты.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Настройте, куда будет перенаправляться пользователь после принятия приглашения.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Выберите отправку стандартного приглашения через нас

    ```
    "sendInvitationMessage": true
    ```

  с сообщением для получателя, которое можно настроить.

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Выберите также, кому отправить копию. Это могут быть пользователи, которых вы хотите известить о приглашении данного сотрудника.

5. Или полностью настройте свой вариант приглашения и рабочего процесса регистрации сотрудников. При этом уведомления через Azure AD не будут отсылаться.

    ```
    "sendInvitationMessage": false
    ```

  В этом случае вы получите URL-адрес активации из API, который можно вставить в шаблон электронного сообщения, отправить в мгновенном сообщении или распространить другим способом на свое усмотрение.

6. Наконец, если вы являетесь администратором, то вы можете пригласить пользователя в качестве участника.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Модель авторизации
API можно запустить в следующих режимах авторизации:

### <a name="app--user-mode"></a>Приложение + пользователь
В этом режиме приложение или пользователь, использующий API, должен иметь разрешения на создание приглашений в службу B2B.

### <a name="app-only-mode"></a>Только приложение
В контексте "только приложение" для успешного выполнения приглашения приложению требуется область User.ReadWrite.All или Directory.ReadWrite.All.

Дополнительные сведения: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Чтобы с легкостью добавлять и приглашать в организацию внешних пользователей, теперь можно использовать PowerShell. Создайте новое приглашение, используя приведенный ниже командлет.

```
New-AzureADMSInvitation
```

Можно использовать приведенные ниже параметры.

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Можно также ознакомиться со справочником по API приглашения: [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)


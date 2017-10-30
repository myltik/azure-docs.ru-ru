---
title: "Как добавить имеющуюся подписку Azure в каталог Azure AD | Документация Майкрософт"
description: "Как добавить имеющуюся подписку в каталог Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Как добавить подписку Azure в Azure Active Directory
Из этой статьи вы узнаете о связи между подпиской Azure и службой Azure Active Directory (Azure AD) и о том, как добавить существующую подписку в каталог Azure AD. Подписка Azure связана с Azure AD отношением доверия. Это означает, что подписка доверяет каталогу проверку подлинности пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. 

Это отношение доверия между подпиской и каталогом отличается от связи подписки с другими ресурсами в Azure (веб-сайтами, базами данных и т. д.). Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Но каталог Azure AD остается в Azure, и вы можете связать другую подписку с этим каталогом и управлять им, используя новую подписку.

Все пользователи относятся к единому домашнему каталогу, который проверяет их подлинность. Они также могут быть гостями в других каталогах. В Azure AD отображаются только каталоги, участником или гостем которых вы являетесь.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Добавление существующей подписки в каталог Azure AD
Для входа необходимо использовать учетную запись, которая существует в текущем каталоге, связанном с подпиской, и в каталоге, в который необходимо добавить подписку. 

1. Войдите в [Центр управления учетной записью Azure](https://account.azure.com/Subscriptions) с помощью данных администратора учетной записи подписки, права на владение которой необходимо передать.
2. Убедитесь, что пользователь, которому вы хотите передать права на владение, находится в целевом каталоге.
3. Щелкните **Перенос подписки**.
4. Укажите получателя. Получателю автоматически приходит сообщение электронной почты со ссылкой, по которой он может принять подписку.
5. Получатель переходит по ссылке и следует инструкциям, в том числе вводит свои данные для оплаты. Подписка передается после успешного выполнения всех действий получателем. 
6. Каталог по умолчанию для подписки изменится на каталог, в котором находится пользователь.

Дополнительные сведения см. в статье [Передача прав владения подпиской Azure другой учетной записи](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об изменении администратора подписки Azure см. в статье [Передача прав владения подпиской Azure другой учетной записи](../billing/billing-subscription-transfer.md).
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](active-directory-understanding-resource-access.md).
* Дополнительные сведения о назначении ролей в Azure AD см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

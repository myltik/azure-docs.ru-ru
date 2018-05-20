---
title: Акселераторы решений Интернета вещей Azure и Azure Active Directory | Документация Майкрософт
description: В этой статье описывается, как акселераторы решений Интернета вещей Azure используют Azure Active Directory для управления разрешениями.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: b7360ca4df63cac114b0eb1f93375367da6735cc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Разрешения на сайте azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Что происходит при входе

При первом входе сайт [azureiotsuite.com][lnk-azureiotsuite] определяет ваши уровни разрешений в зависимости от выбранного клиента Azure Active Directory (AAD) и подписки Azure.

1. Сначала сайт получает из Azure сведения о ваших клиентах AAD, чтобы заполнить список клиентов, который отображается рядом с вашим именем пользователя после входа. В настоящее время сайт может одновременно получать маркеры пользователя только для одного клиента. Поэтому, если вы переключитесь между клиентами в раскрывающемся списке в правом верхнем углу сайта, вам придется выполнить вход с указанием этого клиента, чтобы получить для него маркеры.

2. После этого сайт получает из Azure сведения о подписках, связанных с выбранным клиентом. Доступные подписки можно просмотреть при создании акселератора решений.

3. Наконец, сайт получает сведения о всех ресурсах в подписках и группах ресурсов, помеченных как акселераторы решений, и заполняет элементы на домашней странице.

В следующих разделах описываются роли, которые позволяют управлять доступом к акселераторам решений.

## <a name="aad-roles"></a>Роли AAD

Роли AAD определяют возможность подготавливать акселераторы решений и управлять пользователями в акселераторе решений.

Дополнительные сведения о ролях администратора в AAD см. в статье [Назначение ролей администратора в Azure AD][lnk-aad-admin]. В этой статье основное внимание уделяется ролям **глобального администратора**, а также **пользовательским** ролям каталога, которые используются в акселераторах решений.

### <a name="global-administrator"></a>Глобальный администратор.

В одном клиенте AAD может быть несколько глобальных администраторов.

* Создавая клиент AAD, вы по умолчанию становитесь его глобальным администратором.
* Глобальный администратор может подготовить базовые и стандартные акселераторы решений.

### <a name="domain-user"></a>Пользователь домена

На каждый клиент AAD может приходиться много пользователей домена.

* Пользователь домена может подготовить базовый акселератор решений на сайте [azureiotsuite.com][lnk-azureiotsuite].
* Пользователь домена может создать базовый акселератор решений с помощью интерфейса командной строки.

### <a name="guest-user"></a>Гостевой пользователь

В одном клиенте AAD может быть много гостевых пользователей. Пользователи-гости обладают ограниченным набором прав в клиенте AAD. Поэтому они не могут подготавливать акселераторы решений в клиенте AAD.

Дополнительные сведения о пользователях и ролях в AAD см. в следующих ресурсах:

* [Добавление новых пользователей или пользователей с учетными записями Майкрософт в Azure Active Directory][lnk-create-edit-users]
* [Назначение пользователя или группы корпоративному приложению в предварительной версии Azure Active Directory][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Роли администратора подписки Azure

Роли администратора Azure определяют возможность сопоставлять подписку Azure с клиентом AD.

Дополнительные сведения о ролях администраторов Azure см. в статье [Добавление или изменение ролей администратора Azure, управляющих подписками и службами][lnk-admin-roles].

## <a name="faq"></a>Часто задаваемые вопросы

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Я являюсь администратором службы и хочу изменить сопоставление каталогов подписки и конкретного клиента AAD. Как выполнить эту задачу?

Ознакомьтесь с разделом [Добавление существующей подписки в каталог Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Я хочу сменить администратора службы и соадминистратора при входе в систему с использованием учетной записи организации

См. справочную статью [Смена администратора службы и соадминистратора при входе в систему с использованием учетной записи организации][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Почему появляется ошибка «У вашей учетной записи нет необходимых разрешений для создания решения. Обратитесь к администратору учетной записи или попробуйте использовать другую учетную запись»

Руководствуйтесь следующей схемой:

![][img-flowchart]

> [!NOTE]
> Вы выполнили проверку, которая подтверждает ваши права глобального администратора на клиенте AAD и соадминистратора подписки, но ошибка не устранена. В таком случае администратор учетной записи должен удалить этого пользователя и повторно назначить необходимые разрешения в указанном порядке. Сначала добавить пользователя в качестве глобального администратора, а затем — пользователя в качестве соадминистратора подписки Azure. Если проблемы не удается устранить, обратитесь в службу [справки и поддержки][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Почему эта ошибка появляется при наличии подписки Azure? "Для создания предварительно настроенных решений требуется подписка Azure. Вы можете создать бесплатную пробную учетную запись всего за несколько минут".

Если точно известно, что у вас есть подписка Azure, проверьте сопоставление клиентов для своей подписки и убедитесь, что в раскрывающемся списке выбран правильный клиент. Если клиент выбран правильно, следуйте приведенной выше схеме и проверьте сопоставление подписки и этого клиента AAD.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше об акселераторах решений Интернета вещей, ознакомьтесь с руководством по [настройке акселератора решений][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

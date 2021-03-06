---
title: Добавление или изменение ролей подписки администратора Azure | Документация Майкрософт
description: Инструкции по добавлению или изменению соадминистратора Azure, администратора службы или администратора учетной записи
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Добавление или изменение администраторов подписки Azure

Администраторы классической подписки Azure и [управления доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) Azure — это две системы управления доступом к ресурсам Azure.

* Роли администратора классической подписки — администратор учетных записей, администратор службы и соадминистратор — обеспечивают управление базовым доступом к активам.
    * При регистрации новой подписки Azure ваша учетная запись по умолчанию получает роль администратора учетных записей и администратора службы.
    * Роль соадминистратора можно добавить уже после входа.
* RBAC — это новая система, которая предлагает низкоуровневое управление доступом с помощью многих встроенных ролей, гибкой настройки области и пользовательских ролей.
    * Тем не менее пользователи, у которых есть только RBAC-роли и нет ролей администратора классической подписки, не могут управлять классическими развертываниями Azure.

Для улучшения контроля и упрощения управления доступом для всех задач по управлению доступом мы рекомендуем использовать RBAC. Если это возможно, рекомендуется перенастроить существующие политики доступа для использования RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Добавление администратора владельца RBAC для подписки на портале Azure 

Чтобы добавить пользователя как администратора для администрирования служб подписки Azure, назначьте ему роль владельца RBAC в подписке. Роль владельца может управлять ресурсами назначенной подписки, но не имеет доступа к другим подпискам.

1. Откройте раздел [**Подписки** на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Выберите подписку, которой нужно предоставить доступ.
3. Выберите **Добавить**.  
   (Если кнопка "Добавить" отсутствует, это значит, что у вас нет прав на добавление разрешений.)
4. В меню выберите **Управление доступом (IAM)**.
5. В поле **Роль** выберите значение **Владелец**. 
6. В поле **Назначение доступа** выберите значение **Пользователь, группа или приложение Azure AD**. 
7. В поле **Выбрать** укажите адрес электронной почты пользователя, для которого хотите добавить роль "Владелец". Выберите пользователя, а затем щелкните **Сохранить**.

    ![Снимок экрана, на котором показана выбранная роль "Владелец"](./media/billing-add-change-azure-subscription-administrator/add-role.png)

После этого пользователь получит полный доступ ко всем ресурсам, включая право делегировать доступ другим пользователям. Чтобы изменить область доступа, например группу ресурсов, откройте меню IAM для данной области. 

## <a name="add-or-change-co-administrator"></a>Добавление или изменение соадминистратора

В качестве соадминистратора можно добавить только владельца. Пользователей с такими ролями, как "участник" и "читатель", невозможно добавить в качестве соадминистратора.

> [!TIP]
> Необходимо только добавить учетную запись владельца в качестве соадминистратора, если пользователю требуется возможность управлять классическими службами Azure. Для всех других целей мы рекомендуем использовать RBAC.

1. Если владелец еще не назначен, сделайте это, следуя инструкциям выше.
2. **Щелкните правой кнопкой мыши** только что добавленного владельца, а затем выберите **Добавить соадминистратора**. Если вы не видите параметр **Добавить соадминистратора**, обновите страницу или попробуйте использовать другой браузер. 

    ![Снимок экрана, на котором показаны элементы для добавления соадминистратора](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Чтобы удалить разрешения соадминистратора, **щелкните правой кнопкой мыши** соадминистратора, а затем выберите **Удалить соадминистратора**.

    ![Снимок экрана, на котором показаны элементы для удаления соадминистратора](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Изменение администратора служб для подписки Azure

Только администратор учетной записи может изменить администратора службы для подписки. По умолчанию для новой подписки администратор учетных записей является также администратором служб. Если роль администратора службы была назначена другому пользователю, администратор учетных записей теряет доступ к порталу Azure. Тем не менее администратор учетных записей всегда может использовать Центр управления учетной записью, чтобы назначить себя администратором служб.

1. Убедитесь, что сценарий поддерживается, просмотрев [ограничения на изменение администраторов служб](#limits).
1. Войдите в [Центр управления учетной записью](https://account.windowsazure.com/subscriptions) в качестве администратора учетной записи.
1. Выберите подписку.
1. В правой части выберите **Изменить сведения о подписке**.

    ![Снимок экрана, отображающий кнопку "Изменить подписку" в Центре управления учетной записью](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. В поле **АДМИНИСТРАТОР СЛУЖБЫ** введите адрес электронной почты нового администратора службы.

    ![Снимок экрана, отображающий поле для изменения электронного адреса администратора служб](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Ограничения для изменения администраторов служб

* Каждая подписка связана с каталогом Azure AD. Чтобы найти каталог, с которым связана подписка, перейдите в раздел [**Подписки**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), а затем выберите подписку и просмотрите каталог.
* Если вы выполняете вход с использованием рабочей или учебной учетной записи, вы можете добавлять другие учетные записи в своей организации в качестве администратора служб. Например, abby@contoso.com может добавить bob@contoso.com в качестве администратора служб, но он не может добавить john@notcontoso.com, если john@notcontoso.com не находится в каталоге contoso.com. Пользователи, выполнившие вход с использованием рабочей или учебной учетной записи, могут и далее добавлять пользователей учетной записи Майкрософт в качестве администратора служб.

  | Метод входа | Возможность добавления пользователя учетной записи Майкрософт в качестве администратора служб | Возможность добавления рабочей или учебной учетной записи в той же организации, к которой относится администратор служб | Возможность добавления рабочей или учебной учетной записи не в той же организации, к которой относится администратор служб |
  | --- | --- | --- | --- |
  |  Учетная запись Майкрософт |Yes |Нет  |Нет  |
  |  Рабочая или учебная учетная запись |Yes |Yes |Нет  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Изменение администратора учетной записи для подписки Azure

Администратор учетной записи — это пользователь, который был изначально зарегистрирован для подписки Azure как владелец подписки, которому выставляются счета. Сведения о том, как изменить администратора учетной записи для подписки, см. в статье [Передача прав владения подпиской Azure другой учетной записи](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Не уверены, кто является администратором учетной записи?** Выполните следующие действия.

1. Откройте раздел [**Подписки** на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Выберите подписку, которую требуется проверить, а затем просмотрите раздел **Параметры**.
1. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи**.  

## <a name="types-of-classic-subscription-admins"></a>Типы администраторов классической подписки

 В Azure предусмотрено три роли администратора классической подписки: администратор учетной записи, администратор службы и соадминистратор. Учетной записи, используемой для регистрации в Azure, автоматически назначается роль администратора учетной записи и администратора службы. Дополнительные роли соадминистратора можно добавить уже позже. В следующей таблице представлены различия между этими тремя ролями администраторов. 

> [!TIP]
> Чтобы улучшить точность и уровень управления доступом, рекомендуется использовать управление доступом на основе ролей Azure (RBAC), что дает возможность назначать пользователям несколько ролей. Дополнительные сведения см. в статье [Контроль доступа на основе ролей в Azure Active Directory](../role-based-access-control/overview.md).

| Классический администратор подписки | Ограничение | ОПИСАНИЕ |
| --- | --- | --- |
| Администратор учетной записи |Один на учетную запись Azure |Это пользователь, который оформил подписку Azure и получил доступ к [Центру управления учетной записью](https://account.azure.com/Subscriptions) и возможность выполнять разные задачи управления. Сюда относится возможность создавать и отменять подписки, менять тарифный план для подписки и изменять администратора службы. По существу администратор учетной записи — это владелец подписки, которому выставляются счета. В RBAC администратор учетной записи не назначает роль.|
| Администратор службы |Один на подписку Azure |Эта роль авторизована управлять службами на [портале Azure](https://portal.azure.com). По умолчанию для новой подписки администратор учетных записей является также администратором службы. В RBAC роль владельца назначается администратору службы на уровне подписки.|
| Соадминистратор |200 на подписку |Эта роль имеет те же права доступа, что и администратор службы, но не может менять связь подписок с каталогами Azure. В RBAC роль владельца назначается соадминистратору на уровне подписки.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Дополнительные сведения о контроле доступа к ресурсам и Active Directory

* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в [этой статье](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения об Azure Active Directory см. в статьях [Связь между подписками Azure и службой Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) и [Назначение ролей администратора в Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

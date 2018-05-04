---
title: Создание удостоверения для приложения Azure на портале | Документация Майкрософт
description: Описывается создание нового приложения Azure Active Directory и субъекта-службы, которые можно использовать в сочетании с контролем доступа на основе ролей в Azure Resource Manager для управления доступом к ресурсам.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: bbda406633f97d9a6c90bc49374268df28b68f2a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала

При наличии кода, для которого требуется права на доступ к ресурсам или их изменение, необходимо настроить приложение Azure Active Directory (AD). Назначьте приложению AD необходимые разрешения. Этот подход предпочтительнее запуска приложения с вашими учетными данными, потому что для удостоверения приложения вы можете назначить разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.

В этой статье рассказывается, как это сделать с помощью портала Azure. Здесь рассматривается однотенантное приложение — решение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.

> [!IMPORTANT]
> Вместо создания субъекта-службы вы можете применить управляемое удостоверение службы (MSI) Azure AD в качестве удостоверения приложения. MSI Azure AD используется в режиме общедоступной предварительной версии. Эта функция Azure Active Directory упрощает создание удостоверения для кода. Если код выполняется в службе, которая поддерживает MSI Azure AD и обращается к ресурсам, которые поддерживают аутентификацию Azure Active Directory, то MSI Azure AD будет оптимальным выбором. Дополнительные сведения об удостоверении MSI Azure AD, в том числе список поддерживаемых служб, см. в статье [Управляемое удостоверение службы (MSI) для ресурсов Azure](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Необходимые разрешения

В целях этой статьи у вас должны быть права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Давайте убедимся, что у вас есть необходимые права на выполнение этих шагов.

### <a name="check-azure-active-directory-permissions"></a>Проверка разрешений в Azure Active Directory

1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. В Azure Active Directory выберите **Параметры пользователя**.

   ![Выбор параметров пользователя](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Проверьте параметр **Регистрация приложений**. Если здесь указано значение **Да**, пользователи без прав администратора могут регистрировать приложения в Active Directory. Это означает, что приложение в клиенте Azure AD может зарегистрировать любой пользователь. В таком случае можно выполнить [проверку прав доступа к подпискам Azure](#check-azure-subscription-permissions).

   ![Проверка регистрации приложений](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Если для параметра регистрации приложений указано значение **Нет**, это значит, что только [глобальные администраторы](../active-directory/active-directory-assign-admin-roles-azure-portal.md) могут регистрировать приложения. Проверьте, назначена ли для вашей учетной записи роль администратора клиента Azure AD. Выберите **Обзор** и просмотрите сведения о пользователе. Если вашей учетной записи назначена роль пользователя, а параметр регистрации приложений (который мы проверили на предыдущем этапе) разрешает регистрацию только администраторам, обратитесь к глобальному администратору с просьбой назначить вам роль администратора или предоставить пользователям возможность регистрировать приложения.

   ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Проверка прав доступа к подпискам Azure

Чтобы вы могли назначить роль приложению Active Directory, вашей учетной записи в подписке Azure должно быть предоставлено разрешение `Microsoft.Authorization/*/Write`. Это разрешение предоставляется ролью [владельца](../role-based-access-control/built-in-roles.md#owner) или [администратора доступа пользователей](../role-based-access-control/built-in-roles.md#user-access-administrator). Если вашей учетной записи назначена роль **участник**, значит у вас нет соответствующего разрешения. При попытке назначить роль субъекту-службе вы увидите ошибку.

Чтобы проверить права доступа к подписке, выполните следующие действия.

1. В верхнем правом углу выберите свою учетную запись, а затем щелкните **Мои разрешения**.

   ![Выбор разрешений пользователя](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. В раскрывающемся списке выберите подписку. Выберите ссылку **Щелкните здесь, чтобы просмотреть полные сведения о доступе для этой подписки**.

   ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/view-details.png)

1. Просмотрите назначенные вам роли и определите, есть ли у вас разрешение назначать роли приложению Active Directory. Если разрешения нет, обратитесь к администратору подписки с просьбой назначить вам роль администратора доступа пользователей. На следующем рисунке представлен пользователь, которому назначена роль владельца. Это значит, что у такого пользователя есть необходимые разрешения.

   ![Просмотр разрешений](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com).
1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Щелкните **Регистрация приложений**.

   ![Пункт "Регистрация приложений"](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Выберите **Регистрация нового приложения**.

   ![Действие "Добавить приложение"](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Укажите имя и URL-адрес для приложения. В качестве типа создаваемого приложения выберите **Веб-приложение или API**. Вы не можете создавать учетные данные для [собственных приложений](../active-directory/active-directory-application-proxy-native-client.md), поэтому этот тип не подходит для автоматизированного приложения. Выбрав нужные значения, нажмите кнопку **Создать**.

   ![указание имени приложения](./media/resource-group-create-service-principal-portal/create-app.png)

Приложение создано.

## <a name="get-application-id-and-authentication-key"></a>Получение идентификатора приложения и ключа проверки подлинности

При программном входе необходимо указывать идентификатор приложения и ключ проверки подлинности. Получить эти значения можно следующим образом.

1. В Azure Active Directory в разделе **Регистрация приложений** выберите нужное приложение.

   ![Выбор приложения](./media/resource-group-create-service-principal-portal/select-app.png)

1. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Некоторые [примеры приложений](#log-in-as-the-application) ссылаются на это значение как на идентификатор клиента.

   ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Чтобы создать ключ проверки подлинности, щелкните **Параметры**.

   ![Выбор элемента "Параметры"](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Чтобы создать ключ проверки подлинности, щелкните **Ключи**.

   ![Пункт "Ключи"](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Введите описание и срок действия ключа. Затем нажмите кнопку **Сохранить**.

   ![Сохранение ключа](./media/resource-group-create-service-principal-portal/save-key.png)

   После этого отобразится значение ключа. Это значение нельзя будет получить позже, поэтому скопируйте его сразу. Это значение необходимо предоставить вместе с идентификатором приложения для входа от имени приложения. Сохраните значение ключа, чтобы приложение могло получить к нему доступ.

   ![сохраненный ключ](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Получение идентификатора клиента

При программном входе необходимо передать идентификатор клиента в запросе на проверку подлинности.

1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Чтобы получить идентификатор клиента, щелкните **Свойства** для клиента Azure AD.

   ![Выбор свойств Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Скопируйте **идентификатор каталога**. Это и есть ваш идентификатор клиента.

   ![tenant ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Назначение роли приложению

Чтобы обеспечить доступ к ресурсам в подписке, необходимо назначить приложению роль. Укажите, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](../role-based-access-control/built-in-roles.md).

Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы.

1. Перейдите на уровень области действия, которому вы хотите назначить приложение. Например, чтобы назначить роль в области действия подписки выберите **Подписки**. Или же вы можете выбрать группу ресурсов либо отдельный ресурс.

   ![выбрать подписку](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Выберите определенную подписку, группу ресурсов или ресурс, которым будет назначено приложение.

   ![выбрать подписку для назначения](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Выберите **Управление доступом (IAM)**.

   ![выбрать доступ](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Выберите **Добавить**.

   ![выбрать "добавить"](./media/resource-group-create-service-principal-portal/select-add.png)

1. Выберите роль, которая будет назначена приложению. На следующем изображении представлена роль **Читатель**.

   ![выбрать роль](./media/resource-group-create-service-principal-portal/select-role.png)

1. По умолчанию приложения Azure Active Directory не отображаются среди доступных вариантов. Чтобы найти приложение, укажите его имя в поле поиска. Выберите его.

   ![Поиск приложения](./media/resource-group-create-service-principal-portal/search-app.png)

1. Выберите **Сохранить**, чтобы завершить назначение роли. Вы увидите свое приложение в списке пользователей, назначенных выбранной роли для выбранной области действия.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о настройке мультитенантного приложения см. в статье [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Дополнительные сведения о настройке политик безопасности см. в статье о [контроле доступа на основе ролей Azure](../role-based-access-control/role-assignments-portal.md).  
* Список доступных действий, которые можно разрешить или запретить пользователям, см. в разделе [Операции поставщиков ресурсов Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).

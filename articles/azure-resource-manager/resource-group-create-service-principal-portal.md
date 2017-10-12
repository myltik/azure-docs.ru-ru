---
title: "Создание удостоверения для приложения Azure на портале | Документация Майкрософт"
description: "Описывается создание нового приложения Azure Active Directory и субъекта-службы, которые можно использовать в сочетании с контролем доступа на основе ролей в Azure Resource Manager для управления доступом к ресурсам."
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
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 56b73ff30e7fdaa3c21bc1e5528e2f6118597ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала

При наличии приложения, требующего доступа к ресурсам или разрешения на их изменение, необходимо настроить приложение Azure Active Directory (AD) и назначить ему необходимые разрешения. Этот подход предпочтительнее запуска приложения с вашими учетными данными по следующим причинам:

* Для удостоверения приложения можно назначить разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Не требуется изменять учетные данные приложения в случае изменения ваших обязанностей. 
* Можно использовать сертификат, чтобы автоматизировать аутентификацию при выполнении автоматического сценария.

В этой статье рассказывается, как это сделать с помощью портала. Здесь рассматривается однотенантное приложение — решение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.

## <a name="required-permissions"></a>Необходимые разрешения

Для работы с этой статьей у вас должны быть права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Давайте убедимся, что у вас есть необходимые права на выполнение этих шагов.

### <a name="check-azure-active-directory-permissions"></a>Проверка разрешений в Azure Active Directory

1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com).

1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. В Azure Active Directory выберите **Параметры пользователя**.

   ![Выбор параметров пользователя](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Проверьте параметр **Регистрация приложений**. Если здесь указано значение **Да**, пользователи без прав администратора могут регистрировать приложения в Active Directory. Это означает, что приложение в клиенте Azure AD может зарегистрировать любой пользователь. В таком случае можно выполнить [проверку прав доступа к подпискам Azure](#check-azure-subscription-permissions).

   ![Проверка регистрации приложений](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Если для параметра регистрации приложений указано значение **Нет**, это значит, что только пользователи с правами администратора могут регистрировать приложения. Проверьте, назначена ли для вашей учетной записи роль администратора клиента Azure AD. В разделе "Быстрые задачи" щелкните **Обзор**, а затем выберите **Найти пользователя**.

   ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/find-user.png)

1. Найдите свою учетную запись и выберите ее.

   ![Поиск пользователя](./media/resource-group-create-service-principal-portal/show-user.png)

1. В разделе со сведениями об учетной записи щелкните **Роль каталога**.

   ![Пункт "Роль каталога"](./media/resource-group-create-service-principal-portal/select-directory-role.png)

1. Просмотрите назначенную роль каталога в Azure AD. Если вашей учетной записи назначена роль пользователя, а параметр регистрации приложений (который мы проверили на предыдущем этапе) разрешает регистрацию только администраторам, обратитесь к администратору с просьбой назначить вам роль администратора или включить для пользователей возможность регистрации приложений.

   ![Просмотр роли](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Проверка прав доступа к подпискам Azure

Чтобы вы могли назначить роль приложению Active Directory, вашей учетной записи в подписке Azure должно быть предоставлено разрешение `Microsoft.Authorization/*/Write`. Это разрешение предоставляется ролью [владельца](../active-directory/role-based-access-built-in-roles.md#owner) или [администратора доступа пользователей](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Если вашей учетной записи назначена роль **участник**, значит у вас нет соответствующего разрешения. При попытке назначить роль субъекту-службе вы увидите ошибку.

Чтобы проверить права доступа к подписке, выполните следующие действия.

1. Если вы еще не открыли страницу своей учетной записи Azure AD, щелкните **Azure Active Directory** на панели слева.

1. Найдите свою учетную запись Azure AD. В разделе "Быстрые задачи" щелкните **Обзор**, а затем выберите **Найти пользователя**.

   ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/find-user.png)

1. Найдите свою учетную запись и выберите ее.

   ![Поиск пользователя](./media/resource-group-create-service-principal-portal/show-user.png)

1. Щелкните **Ресурсы Azure**.

   ![Выбор ресурсов](./media/resource-group-create-service-principal-portal/select-azure-resources.png)

1. Просмотрите назначенные вам роли и определите, есть ли у вас разрешение назначать роли приложению Active Directory. Если разрешения нет, обратитесь к администратору подписки с просьбой назначить вам роль администратора доступа пользователей. На следующем рисунке представлен пользователь, которому назначены роли владельца для двух подписок. Это значит, что такой пользователь имеет необходимые разрешения.

   ![Просмотр разрешений](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com).
1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Щелкните **Регистрация приложений**.

   ![Пункт "Регистрация приложений"](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Выберите **Регистрация нового приложения**.

   ![Действие "Добавить приложение"](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Укажите имя и URL-адрес для приложения. Выберите тип создаваемого приложения: **веб-приложение или API** или **собственное приложение**. Выбрав нужные значения, нажмите кнопку **Создать**.

   ![указание имени приложения](./media/resource-group-create-service-principal-portal/create-app.png)

Приложение создано.

## <a name="get-application-id-and-authentication-key"></a>Получение идентификатора приложения и ключа проверки подлинности

При программном входе необходимо указывать идентификатор приложения и ключ проверки подлинности. Получить эти значения можно следующим образом.

1. В Azure Active Directory в разделе **Регистрация приложений** выберите нужное приложение.

   ![Выбор приложения](./media/resource-group-create-service-principal-portal/select-app.png)

1. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Приложения в разделе [примеров приложений](#sample-applications) используют это значение как идентификатор клиента.

   ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/copy-app-id.png)

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

Чтобы обеспечить доступ к ресурсам в подписке, необходимо назначить приложению роль. Укажите, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md).

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

1. Найдите приложение и выберите его.

   ![Поиск приложения](./media/resource-group-create-service-principal-portal/search-app.png)

1. Выберите **Сохранить**, чтобы завершить назначение роли. Вы увидите свое приложение в списке пользователей, назначенных выбранной роли для выбранной области действия.

## <a name="log-in-as-the-application"></a>Вход с учетной записью приложения

Настройка приложения в Azure Active Directory завершена. У вас есть идентификатор и ключ для входа от имени приложения. Приложению назначена роль, которая разрешает выполнять определенные действия. Сведения о входе в систему в качестве приложения с помощью различных платформ см. в следующих разделах:

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli)
* [REST](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о настройке мультитенантного приложения см. в статье [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Дополнительные сведения о настройке политик безопасности см. в статье о [контроле доступа на основе ролей Azure](../active-directory/role-based-access-control-configure.md).  
* Список доступных действий, которые можно разрешить или запретить пользователям, см. в разделе [Операции поставщиков ресурсов Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

---
title: "Создание субъекта-службы на портале | Документация Майкрософт"
description: "Описывается создание нового приложения Active Directory и субъекта-службы, которые можно использовать в сочетании с контролем доступа на основе ролей в диспетчере ресурсов Azure для управления доступом к ресурсам."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4312002b311ec17f175f6eb6bc45fbe1ce7c7a01
ms.openlocfilehash: 3232aa0356353e3856286c38d931543a254fd9fd


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
> * [Портал](resource-group-create-service-principal-portal.md)
>
>

При наличии приложения, требующего доступа к ресурсам или разрешения на их изменение, необходимо настроить приложение Active Directory (AD) и назначить ему необходимые разрешения. В этой статье рассказывается, как это сделать с помощью портала. Здесь рассматривается однотенантное приложение — решение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации.
 
## <a name="required-permissions"></a>Необходимые разрешения
Для работы с этой статьей у вас должны быть права на регистрацию приложения в Active Directory и назначение приложению роли в подписке Azure. Давайте убедимся, что у вас есть необходимые права на выполнение этих шагов.

### <a name="check-active-directory-permissions"></a>Проверка разрешений в Active Directory
1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com).
2. Выберите **Azure Active Directory**.

     ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. В Active Directory выберите **Параметры пользователя**.

     ![Выбор параметров пользователя](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. Проверьте параметр **Регистрация приложений**. Если здесь указано значение **Да**, пользователи без прав администратора могут регистрировать приложения в Active Directory. Это означает, что приложение в Active Directory может зарегистрировать любой пользователь. В таком случае можно выполнить [проверку прав доступа к подпискам Azure](#check-azure-subscription-permissions).

     ![Проверка регистрации приложений](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. Если для параметра регистрации приложений указано значение **Нет**, это значит, что только пользователи с правами администратора могут регистрировать приложения. В таком случае вам нужно проверить, назначена ли вашей учетной записи роль администратора Active Directory. В разделе "Быстрые задачи" щелкните **Обзор**, а затем выберите **Найти пользователя**.

     ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/find-user.png)
6. Найдите свою учетную запись и выберите ее.

     ![Поиск пользователя](./media/resource-group-create-service-principal-portal/show-user.png)
7. В разделе со сведениями об учетной записи щелкните **Роль каталога**. 

     ![Пункт "Роль каталога"](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Посмотрите, какая роль назначена вам в Active Directory. Если вашей учетной записи назначена роль пользователя, а параметр регистрации приложений (который мы проверили на предыдущем этапе) разрешает регистрацию только администраторам, обратитесь к администратору с просьбой назначить вам роль администратора или включить для пользователей возможность регистрации приложений.

     ![Просмотр роли](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Проверка прав доступа к подпискам Azure
Чтобы вы могли назначить роль приложению Active Directory, вашей учетной записи в подписке Azure должно быть предоставлено разрешение `Microsoft.Authorization/*/Write`. Это разрешение предоставляется ролью [владельца](../active-directory/role-based-access-built-in-roles.md#owner) или [администратора доступа пользователей](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Если вашей учетной записи назначена роль **участник**, значит у вас нет соответствующего разрешения. При попытке назначить роль субъекту-службе вы увидите ошибку. 

Чтобы проверить права доступа к подписке, выполните следующие действия.

1. Если вы еще не открыли страницу своей учетной записи Active Directory, выберите элемент **Azure Active Directory** на панели слева.

2. Найдите свою учетную запись Active Directory. В разделе "Быстрые задачи" щелкните **Обзор**, а затем выберите **Найти пользователя**.

     ![Пункт "Найти пользователя"](./media/resource-group-create-service-principal-portal/find-user.png)
2. Найдите свою учетную запись и выберите ее.

     ![Поиск пользователя](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. Щелкните **Ресурсы Azure**.

     ![Выбор ресурсов](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. Просмотрите назначенные вам роли и определите, есть ли у вас разрешение назначать роли приложению Active Directory. Если разрешения нет, обратитесь к администратору подписки с просьбой назначить вам роль администратора доступа пользователей. На следующем рисунке представлен пользователь, которому назначены роли владельца для двух подписок. Это значит, что такой пользователь имеет необходимые разрешения. 

     ![Просмотр разрешений](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-active-directory-application"></a>Создание приложения Active Directory
1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com).
2. Выберите **Azure Active Directory**.

     ![Выбор Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. Щелкните **Регистрация приложений**.   

     ![Пункт "Регистрация приложений"](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. Выберите **Добавить**.

     ![Действие "Добавить приложение"](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. Укажите имя и URL-адрес для приложения. Выберите тип создаваемого приложения: **веб-приложение или API** или **собственное приложение**. Выбрав нужные значения, нажмите кнопку **Создать**.

     ![указание имени приложения](./media/resource-group-create-service-principal-portal/create-app.png)

Приложение создано.

## <a name="get-application-id-and-authentication-key"></a>Получение идентификатора приложения и ключа проверки подлинности
При программном входе необходимо указывать идентификатор приложения и ключ проверки подлинности. Получить эти значения можно следующим образом.

1. В Active Directory в разделе **регистрации приложений** выберите нужное приложение.

     ![Выбор приложения](./media/resource-group-create-service-principal-portal/select-app.png)
2. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Приложения в разделе [примеров приложений](#sample-applications) используют это значение как идентификатор клиента.

     ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. Чтобы создать ключ проверки подлинности, щелкните **Ключи**.

     ![Пункт "Ключи"](./media/resource-group-create-service-principal-portal/select-keys.png)
4. Введите описание и срок действия ключа. Затем нажмите кнопку **Сохранить**.

     ![Сохранение ключа](./media/resource-group-create-service-principal-portal/save-key.png)

     После этого отобразится значение ключа. Это значение нельзя будет получить позже, поэтому скопируйте его сразу. Это значение необходимо предоставить вместе с идентификатором приложения для входа от имени приложения. Сохраните значение ключа, чтобы приложение могло получить к нему доступ.

     ![сохраненный ключ](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Получение идентификатора клиента
При программном входе необходимо передать идентификатор клиента в запросе на проверку подлинности. 

1. Чтобы получить идентификатор клиента, щелкните **Свойства** для Active Directory. 

     ![Пункт "Свойства" для Active Directory](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. Скопируйте **идентификатор каталога**. Это и есть ваш идентификатор клиента.

     ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Назначение роли приложению
Чтобы обеспечить доступ к ресурсам в подписке, необходимо назначить приложению роль. Укажите, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md).

Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы.

1. Перейдите на уровень области действия, которому вы хотите назначить приложение. Например, чтобы назначить роль в области действия подписки выберите **Подписки**. Или же вы можете выбрать группу ресурсов либо отдельный ресурс.

     ![выбрать подписку](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. Выберите определенную подписку, группу ресурсов или ресурс, которым будет назначено приложение.

     ![выбрать подписку для назначения](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. Выберите **Управление доступом (IAM)**.

     ![выбрать доступ](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. Выберите **Добавить**.

     ![выбрать "добавить"](./media/resource-group-create-service-principal-portal/select-add.png)
6. Выберите роль, которая будет назначена приложению. На следующем изображении представлена роль **Читатель**.

     ![выбрать роль](./media/resource-group-create-service-principal-portal/select-role.png)

8. Найдите приложение и выберите его.

     ![Поиск приложения](./media/resource-group-create-service-principal-portal/search-app.png)
9. Нажмите кнопку **ОК**, чтобы завершить назначение роли. Вы увидите свое приложение в списке пользователей, назначенных выбранной роли для выбранной области действия.

Настройка приложения в Active Directory завершена. У вас есть идентификатор и ключ для входа от имени приложения. Приложению назначена роль, которая разрешает выполнять определенные действия. Чтобы узнать, как выполнять задачи с использованием кода, изучите представленные примеры приложений.

## <a name="sample-applications"></a>Примеры приложений
В следующих примерах приложений показано, как выполнить вход от имени приложения Active Directory.

**.NET**

* [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Управление ресурсами и группами ресурсов Azure с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Приступая к работе с ресурсами: развертывание с помощью шаблона Azure Resource Manager на Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Приступая к работе с ресурсами: управление группой ресурсов на Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Управление ресурсами и группами ресурсов Azure с помощью Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Управление ресурсами и группами ресурсов Azure с помощью Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Управление ресурсами и группами ресурсов Azure с помощью Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о настройке мультитенантного приложения см. в статье [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Дополнительные сведения о настройке политик безопасности см. в статье о [контроле доступа на основе ролей Azure](../active-directory/role-based-access-control-configure.md).  




<!--HONumber=Dec16_HO1-->



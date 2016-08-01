<properties
   pageTitle="Создание субъекта-службы на портале | Microsoft Azure"
   description="Описывается создание нового приложения Active Directory и субъекта-службы, которые можно использовать в сочетании с контролем доступа на основе ролей в диспетчере ресурсов Azure для управления доступом к ресурсам."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)


При наличии приложения, требующего доступа к ресурсам или разрешения на их изменение, необходимо настроить приложение Active Directory (AD) и назначить ему необходимые разрешения. В этой статье рассказывается, как это сделать с помощью портала. В настоящее время роли приложения Active Directory необходимо создать на классическом портале, а затем перейти на портал Azure и назначить приложению роль.

> [AZURE.NOTE] Настроить приложение AD и субъект-службы будет проще через [PowerShell](resource-group-authenticate-service-principal.md) или [Azure CLI](resource-group-authenticate-service-principal-cli.md), особенно если необходимо использовать проверку подлинности на основе сертификата. В этой статье не показано, как использовать сертификат.

Пояснения к терминам Active Directory см. в статье [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).

## Создание приложения Active Directory

1. Войдите в свою учетную запись Azure через [классическую версию портала](https://manage.windowsazure.com/).

2. Выберите **Active Directory** на левой панели.

     ![выбор Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Выберите каталог Active Directory, который хотите использовать для создания нового приложения. Если у вас есть несколько каталогов Active Directory, приложение необходимо создать в том же каталоге, где находится ваша подписка. Доступ можно предоставлять только к тому ресурсу в подписке на приложение, который находится в том же каталоге, что и ваша подписка.

     ![выбор каталога](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    Чтобы определить каталог для подписки, выберите пункт **Параметры** и найдите имя каталога.
   
     ![поиск каталога по умолчанию](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.

     ![просмотр приложений](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Если ранее вы не создавали приложений в этом каталоге, вы увидите примерно следующее: Щелкните **ДОБАВИТЬ ПРИЛОЖЕНИЕ**

     ![добавление приложения](./media/resource-group-create-service-principal-portal/create-application.png)

     Также можно щелкнуть **Добавить** на нижней панели.

     ![добавление](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Выберите тип приложения, которое вы хотите создать. Для работы с данным руководством выберите **Добавить приложение, разрабатываемое моей организацией**.

     ![новое приложение](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Укажите имя и тип приложения. Для работы с данным руководством выберите **ВЕБ-ПРИЛОЖЕНИЕ И/ИЛИ ВЕБ-API**, а затем нажмите кнопку "Далее". При выборе параметра **СОБСТВЕННОЕ КЛИЕНТСКОЕ ПРИЛОЖЕНИЕ** дальнейшие действия будут отличаться от описанных в статье.

     ![указание имени приложения](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Задайте свойства приложения. В поле **URL-АДРЕС ВХОДА** введите URI веб-сайта, который описывает ваше приложение. Существование этого сайта не проверяется. В поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ** введите URI, который идентифицирует ваше приложение.

     ![свойства приложения](./media/resource-group-create-service-principal-portal/app-properties.png)

Приложение создано.

## Получение идентификатора клиента и ключа для проверки подлинности

При программном входе необходимо указывать идентификатор приложения. Если приложение выполняется под собственными учетными данными, потребуется также ключ проверки подлинности.

1. Откройте вкладку **Настройка**, чтобы задать для него пароль.

     ![настройка приложения](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Скопируйте значение в поле **ИДЕНТИФИКАТОР КЛИЕНТА**.
  
     ![идентификатор клиента](./media/resource-group-create-service-principal-portal/client-id.png)

3. Если приложение будет выполняться под собственными учетными данными, прокрутите окно до раздела **Ключи** и выберите срок действия пароля.

     ![ключи](./media/resource-group-create-service-principal-portal/create-key.png)

4. Нажмите **Сохранить**, чтобы сохранить созданный ключ.

     ![сохранение](./media/resource-group-create-service-principal-portal/save-icon.png)

     Сохранный ключ появится на экране, и вы сможете его скопировать. Позже получить ключ будет невозможно, поэтому стоит скопировать его сейчас.

     ![сохраненный ключ](./media/resource-group-create-service-principal-portal/save-key.png)

## Получение идентификатора клиента

При программном входе в запросе проверки подлинности необходимо передавать идентификатор клиента. Чтобы получить идентификатор клиента для веб-приложений и веб-приложений API, внизу экрана нужно выбрать **Просмотр конечных точек** и найти идентификатор, как показано ниже.

   ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/save-tenant.png)

Идентификатор клиента можно также получить через PowerShell:

    Get-AzureRmSubscription

Или с помощью Azure CLI.

    azure account show --json

## Настройка делегированных разрешений

Если приложение получает доступ к ресурсам от имени выполнившего вход пользователя, необходимо предоставить приложению делегированное разрешение для доступа к другим приложениям. Это можно сделать на вкладке **Настройка** в разделе **Разрешения для других приложений**. По умолчанию делегированное разрешение для Azure Active Directory уже включено. Оставьте это делегированное разрешение без изменений.

1. Выберите **Добавить приложение**.

2. Из списка выберите **Azure Service Management API** (API управления службами Azure). Затем щелкните значок завершения.

      ![выбор приложения](./media/resource-group-create-service-principal-portal/select-app.png)

3. В раскрывающемся списке делегированных разрешений установите флажок **Access Azure Service Management as organization** (Доступ к управлению службами Azure в качестве организации).

      ![выбор разрешения](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Сохраните изменения.

## Настройка мультитенантного приложения

Если пользователи из других каталогов Active Directory могут давать согласие на использование приложения и входить в него, необходимо включить мультитенантность. На вкладке **Настройка** для параметра **Приложение является мультитенантным** задайте значение **Да**.

![мультитенантность](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Назначение роли приложению

Если приложение выполняется под собственными учетными данными, ему необходимо назначить роль. Необходимо решить, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md).

Чтобы назначить роль, требуется доступ `Microsoft.Authorization/*/Write`, который предоставляется с помощью роли [Владелец](./active-directory/role-based-access-built-in-roles.md#owner) или [Администратор доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуются на более низких уровнях области действия (например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы).

1. Чтобы назначить приложение роли, переключитесь с классического портала на [портал Azure](https://portal.azure.com).

1. На портале перейдите на уровень области, которому вы хотите назначить приложение. В рамках данной темы вы можете перейти в группу ресурсов, а затем в колонке группы ресурсов выбрать значок **Доступ**.

     ![Выбор пользователей](./media/resource-group-create-service-principal-portal/select-users.png)

2. Выберите **Добавить**.

     ![выбрать "добавить"](./media/resource-group-create-service-principal-portal/select-add.png)

3. Выберите роль **Читатель** (или любую другую роль, которой вы хотите назначить приложение).

     ![выбрать роль](./media/resource-group-create-service-principal-portal/select-role.png)

4. Когда вы увидите список пользователей, которых можно добавить к роли, приложения будут отсутствовать. Вы увидите только группы и пользователей.

     ![показать пользователей](./media/resource-group-create-service-principal-portal/show-users.png)

5. Чтобы найти приложение, необходимо выполнить его поиск. Начните вводить имя приложения, и список доступных параметров изменится. Выберите приложение, когда увидите его в списке.

     ![назначить роли](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Нажмите кнопку **ОК** для завершения назначения роли. Теперь вы должны видеть свое приложение в списке пользователей, назначенных роли для группы ресурсов.

     ![показать](./media/resource-group-create-service-principal-portal/show-app.png)

Подробнее о назначении пользователям и приложениям ролей с помощью портала см. в разделе [Управление доступом с помощью портала управления Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Примеры приложений

Следующие примеры приложений демонстрируют вход в качестве субъекта-службы.

**.NET**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Приступая к работе с ресурсами: развертывание с помощью шаблона Azure Resource Manager на Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Приступая к работе с ресурсами: управление группой ресурсов на Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Дальнейшие действия

- Дополнительные сведения о настройке политик безопасности см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).
- Видеодемонстрация этих шагов представлена в статье [Включение программного управления ресурсов Azure с помощью Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0720_2016-->
---
title: Настройка утверждения роли в Azure Active Directory, выдаваемого в токене SAML для корпоративных приложений | Документация Майкрософт
description: Узнайте, как настроить в Azure Active Directory утверждение роли, выдаваемое в токене SAML для корпоративных приложений.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 88a9f5988d1fe3f4de4fe10da23a5f713e3f3370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Настройка утверждения роли в Azure Active Directory, выдаваемого в токене SAML для корпоративных приложений

Эта функция позволяет пользователям настроить тип утверждения для утверждений ролей в токене ответа, который возвращается при авторизации приложения в Azure AD.

## <a name="prerequisites"></a>предварительным требованиям
- Подписка Azure AD с настроенным каталогом.
- Подписка с поддержкой единого входа.
- Приложение с настроенным единым входом.

## <a name="when-to-use-this-feature"></a>Когда следует использовать эту функцию

Эта функция понадобится вам, если нужно передавать в приложение пользовательские роли в ответе SAML. С ее помощью вы сможете создать любое количество ролей и передавать их в приложение из Azure AD.

## <a name="steps-to-use-this-feature"></a>Указания по использованию этой функции

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите имя приложения, выберите нужное приложение на панели результатов и щелкните **Добавить**, чтобы добавить это приложение.

    ![Приложение в списке результатов](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Добавив приложение, перейдите к странице **Свойства** и скопируйте **идентификатор объекта**.

    ![Страница "Свойства"](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. В другом окне откройте [песочницу Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    a. Войдите на сайт песочницы Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

    Б. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Извлеките свойство appRoles из объекта субъекта-службы.

    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    д. Теперь вам следует создать новые роли для приложения. Вы можете скачать генератор ролей Azure AD на [этой странице](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Откройте генератор Azure AD и выполните следующие действия.

    ![Генератор Azure AD](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Введите **имя роли**, **описание роли** и **Значение роли**. Чтобы добавить роль, щелкните **Добавить**.
    
    Добавив все необходимые роли, щелкните **Создать**.
    
    Скопируйте содержимое, нажав кнопку **Копировать содержимое**.

    > [!NOTE] 
    > Убедитесь, что роль пользователя **msiam_access** создана и ее идентификатор правильно указан в созданной роли.

    ж. Вернитесь в песочницу Graph. Измените метод **GET** на **PATCH**. Примените к объекту субъекта-службы нужные роли, включив скопированные значения в свойство appRoles. Щелкните **Выполнить запрос**.

    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Ниже приведен пример объекта appRoles. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Добавив новые роли в субъект-службу, вы сможете назначить эти роли пользователям. Для этого перейдите на портал и найдите нужное приложение. Затем щелкните вкладку **Пользователи и группы** в верхней части окна. Здесь вы увидите полный список пользователей и групп.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Чтобы назначить роль, просто выберите нужного пользователя или группу и нажмите кнопку **Назначить** в нижней части страницы.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    Б. Это действие открывает всплывающее окно, где вы сможете выбрать роль из числа тех, которые определены для соответствующего субъекта-службы.

    c. Выберите нужную роль и щелкните "Отправить".

8. Назначив пользователям роли, обновите таблицу **Атрибуты**, чтобы определить настраиваемое сопоставление для утверждения **роли**.

9. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | -------------- | ----------------|    
    | Имя роли      | user.assignedrole |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Настройка атрибута единого входа](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

10. Чтобы проверить работу приложения с единым входом, инициированным поставщиком удостоверений, войдите на панель доступа (https://myapps.microsoft.com) и щелкните плитку этого приложения. Вы увидите токен SAML со списком ролей, назначенных пользователю, с указанным именем утверждения.

## <a name="update-existing-role"></a>Обновление существующей роли

1. Чтобы обновить существующую роль, выполните следующие действия.

    a. В другом окне откройте [песочницу Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    Б. Войдите на сайт песочницы Graph с учетными данными глобального администратора или соадминистратора вашего клиента.
    
    c. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    д. Извлеките свойство appRoles из объекта субъекта-службы.
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Чтобы обновить существующую роль, выполните следующие действия.

    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Измените метод **GET** на **PATCH**.

    * Скопируйте существующие роли из приложения и вставьте их в **текст запроса**.
    
    * Обновите значение роли, указав новые значения **описания роли**, **значения роли** и **отображаемого имени роли** в соответствии с требованиями вашей организации.
    
    * Обновив все необходимые роли, щелкните **Выполнить запрос**.
        
## <a name="delete-existing-role"></a>Удаление существующей роли

1. Чтобы удалить существующую роль, выполните следующие действия.

    a. В другом окне откройте [песочницу Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    Б. Войдите на сайт песочницы Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

    c. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    д. Извлеките свойство appRoles из объекта субъекта-службы.
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Чтобы удалить существующую роль, выполните следующие действия.

    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Измените метод **GET** на **PATCH**.

    Скопируйте существующие роли из приложения и вставьте их в **текст запроса**.
    
    Присвойте значение **false** параметру **IsEnabled** для той роли, которую вы хотите удалить.

    Щелкните **Выполнить запрос**.
    
    > [!NOTE] 
    > Убедитесь, что роль пользователя **msiam_access** создана и ее идентификатор правильно указан в созданной роли.
    
    ж. Выполнив описанный выше процесс, сохраните выбранный метод **PATCH**, вставьте остальное содержимое роли в поле **Текст запроса** и щелкните **Выполнить запрос**.
    
    ![Диалоговое окно песочницы Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Роль будет удалена после выполнения запроса.
    
    > [!NOTE]
    > Перед удалением роль необходимо отключить. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные инструкции см. в [документации по приложениям](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png

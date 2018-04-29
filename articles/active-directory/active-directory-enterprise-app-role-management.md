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
ms.openlocfilehash: 3acfa51351ac49456f5f9fcac8aa4f4f339b9ea3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
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

6. В другом окне откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    a. Войдите на сайт обозревателя Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

    Б. У вас должно быть достаточно разрешений для создания ролей. Щелкните **Изменить разрешения**, чтобы получить требуемые разрешения. 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Выберите следующие разрешения из списка (если у вас их еще нет) и щелкните "Изменить разрешения". 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Вам необходимо снова войти в систему и принять запрос. После принятия запроса вы снова войдете в систему.

    д. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    ж. Извлеките свойство appRoles из объекта субъекта-службы. 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > При использовании пользовательского приложения (не из коллекции) отображаются две роли по умолчанию — user и msiam_access. В случае использования приложения из коллекции роль msiam_access является единственной ролью по умолчанию. Вам не нужно изменять роли по умолчанию.

    h. Теперь вам следует создать роли для приложения. 

    i. Расположенный ниже JSON является примером объекта appRoles. Создайте похожий объект, чтобы добавить необходимые роли в приложение. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ],
    }
    ```
    > [!Note]
    > Вы можете добавлять новые роли только после **msiam_access** для операции исправления. Вы можете добавить столько ролей, сколько нужно вашей организации. Azure Active Directory будет отправлять **значение** этих ролей в качестве значения утверждения в ответе SAML.
    
    j. Вернитесь к своему обозревателю Graph и измените метод с **GET** на **PATCH**. Примените к объекту субъекта-службы нужные роли, обновив свойство appRoles, как показано в примере выше. Щелкните **Выполнить запрос**, чтобы выполнить операцию исправления. Сообщение об успешном выполнении подтверждает создание роли.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Добавив новые роли в субъект-службу, вы сможете назначить эти роли пользователям. Для этого перейдите на портал и найдите нужное приложение. Щелкните вкладку **Пользователи и группы** в верхней части окна. Здесь вы увидите полный список пользователей и групп, которые уже назначены приложению. Вы можете добавить новых пользователей в новую роль, а также выбрать имеющегося пользователя и изменить его роль, нажав кнопку **Изменить**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Чтобы присвоить роль пользователю, выберите новую роль и нажмите кнопку **Присвоить** в нижней части страницы.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Обратите внимание, что вам необходимо обновить сеанс на портале Azure, чтобы увидеть новые роли.

8. Назначив пользователям роли, обновите таблицу **Атрибуты**, чтобы определить настраиваемое сопоставление для утверждения **роли**.

9. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | -------------- | ----------------|    
    | Имя роли      | user.assignedrole |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Настройка атрибута единого входа](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    Б. При необходимости введите имя атрибута в текстовом поле **Имя**. В этом примере в качестве имени утверждения мы использовали **имя роли**.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

10. Чтобы проверить работу приложения с единым входом, инициированным поставщиком удостоверений, войдите на панель доступа (https://myapps.microsoft.com) и щелкните плитку этого приложения. Вы увидите токен SAML со списком ролей, назначенных пользователю, с указанным именем утверждения.

## <a name="update-existing-role"></a>Обновление имеющейся роли

Чтобы обновить имеющуюся роль, выполните следующие действия.

1. Откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Войдите на сайт обозревателя Graph с учетными данными глобального администратора или соадминистратора вашего клиента.
    
3. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Извлеките свойство appRoles из объекта субъекта-службы.
    
    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Чтобы обновить имеющуюся роль, выполните следующие действия.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Измените метод **GET** на **PATCH**.

    * Скопируйте имеющиеся роли и вставьте их в **текст запроса**.

    * Обновите значение роли, при необходимости указав новые значения **описания роли**, **значения роли** или **отображаемого имени роли**.

    * Обновив все необходимые роли, щелкните **Выполнить запрос**.
        
## <a name="delete-existing-role"></a>Удаление имеющейся роли

Чтобы удалить имеющуюся роль, выполните следующие действия.

1. В другом окне откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Войдите на сайт обозревателя Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

3. Выберите **бета-версию** и получите из клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Если вы используете несколько каталогов, следует применить такую модель: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Найдите **идентификатор объекта**, который вы скопировали со страницы свойств, и используйте следующий запрос для получения соответствующего субъекта-службы.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Извлеките свойство appRoles из объекта субъекта-службы.
    
    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Чтобы удалить имеющуюся роль, выполните следующие действия.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Измените метод **GET** на **PATCH**.

    * Скопируйте имеющиеся роли из приложения и вставьте их в **текст запроса**.
        
    * Присвойте значение **false** параметру **IsEnabled** для той роли, которую вы хотите удалить.

    * Щелкните **Выполнить запрос**.
    
    > [!NOTE] 
    > Убедитесь, что роль пользователя **msiam_access** создана и ее идентификатор правильно указан в созданной роли.
    
7. После отключения роли удалите этот блок ролей из раздела appRoles. Оставьте метод **PATCH** и щелкните **Выполнить запрос**.
    
8. Роль будет удалена после выполнения запроса.
    
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

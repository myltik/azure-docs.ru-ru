---
title: Настройка утверждения роли в Azure Active Directory, выдаваемого в токене SAML для корпоративных приложений | Документация Майкрософт
description: Узнайте, как настроить в Azure Active Directory утверждение роли, выдаваемое в токене SAML для корпоративных приложений
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5aa716f91a3155e81ef8dc7c436b4a9a5811238b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34723258"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Настройка утверждения роли в Azure Active Directory, выдаваемого в токене SAML для корпоративных приложений

С помощью Azure Active Directory (Azure AD) вы можете настроить тип утверждения для утверждения роли в токене ответа, который вы получаете после авторизации приложения.

## <a name="prerequisites"></a>предварительным требованиям
- Подписка Azure AD с настроенным каталогом.
- Подписка с поддержкой единого входа (SSO). Настроенный единый вход в приложении.

## <a name="when-to-use-this-feature"></a>Когда следует использовать эту функцию

Эта функция понадобится вам для передачи в приложение пользовательских ролей в ответе SAML. Вы сможете создать любое число ролей и передать их обратно в приложение из Azure AD.

## <a name="create-roles-for-an-application"></a>Создание ролей для приложения

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Значок Azure Active Directory][1]

2. Выберите **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите имя приложения, а затем выберите нужное приложение на панели результатов. Нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Приложение в списке результатов](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Добавив приложение, перейдите на страницу **Свойства** и скопируйте идентификатор объекта.

    ![Страница "Свойства"](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. В другом окне откройте [песочницу Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) и сделайте следующее:

    a. Войдите на сайт песочницы Graph с помощью учетных данных глобального администратора или соадминистратора вашего клиента.

    Б. У вас должны быть разрешения на создание ролей. Щелкните **Изменить разрешения**, чтобы получить разрешения. 

      ![Кнопка "Изменить разрешения"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Выберите следующие разрешения из списка (если у вас их еще нет) и нажмите кнопку **Изменить разрешения**. 

      ![Список разрешений и кнопка "Изменить разрешения"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Дайте согласие. Вы повторно войдете в систему.

    д. Выберите **бета-версию** и получите от клиента список субъектов-служб с помощью следующего запроса:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Если вы используете несколько каталогов, следуйте такому шаблону: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Диалоговое окно песочницы Graph с запросом на получение субъектов-служб](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. В полученном списке найдите субъект-службу, в который нужно внести изменения. Для поиска приложений в списке субъектов-служб можно также использовать клавиши CTRL+F. Найдите идентификатор объекта, скопированный со страницы **Свойства**, и используйте следующий запрос, чтобы получить соответствующий субъект-службу:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Запрос на получение субъекта-службы, который необходимо изменить](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    ж. Извлеките свойство **appRoles** из объекта субъекта-службы. 

      ![Сведения о свойстве appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Если используется пользовательское приложение (не из Azure Marketplace), отображаются две роли по умолчанию: user и msiam_access. В случае использования приложения из Marketplace msiam_access является единственной ролью по умолчанию. Не нужно изменять роли по умолчанию.

    h. Создайте роли для вашего приложения. 

      Ниже приведен документ JSON, представляющий собой пример объекта **appRoles**. Создайте похожий объект, чтобы добавить необходимые роли в приложение. 

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
      ]
      }
      ```
      
      > [!Note]
      > Вы можете добавлять новые роли только после msiam_access для операции исправления. Кроме того, вы можете добавить столько ролей, сколько необходимо вашей организации. Azure AD будет отправлять значение этих ролей в качестве значения утверждения в ответе SAML. Чтобы создать значения GUID для идентификатора новых ролей, используйте веб-инструменты, например [этот](https://www.guidgenerator.com/).
    
    i. Вернитесь в песочницу Graph и измените метод **GET** на **PATCH**. Примените к объекту субъекта-службы нужные роли, обновив свойство **appRoles**, как показано в примере выше. Нажмите кнопку **Запуск запроса**, чтобы выполнить операцию исправления. Сообщение об успешном выполнении подтверждает создание роли.

      ![Операция исправления с сообщением об успешном выполнении](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Добавив новые роли в субъект-службу, вы сможете назначить эти роли пользователям. Вы можете назначать пользователей. Для этого перейдите на портал и откройте приложение. Выберите вкладку **Пользователи и группы**. Отобразится полный список пользователей и групп, которые уже назначены приложению. Вы можете назначать новым пользователям новые роли. Кроме того, можно выбрать существующего пользователя и изменить его роль, щелкнув **Изменить**.

    ![Вкладка "Пользователи и группы"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Чтобы назначить роль любому пользователю, выберите новую роль и нажмите кнопку **Назначить** в нижней части страницы.

    ![Области "Изменение назначения" и "Выбор роли"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Необходимо обновить сеанс на портале Azure, чтобы отобразились новые роли.

8. Обновите таблицу **Атрибуты**, чтобы определить настраиваемое сопоставление утверждения роли.

9. В диалоговом окне **Единый вход** в разделе **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке, и сделайте следующее.
    
    | Имя атрибута | Значение атрибута |
    | -------------- | ----------------|    
    | Имя роли      | user.assignedrole |

    a. Выберите **Добавить атрибут**, чтобы открыть область **Добавление атрибута**.

      ![Кнопка "Добавить атрибут"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Область "Добавление атрибута"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    Б. При необходимости введите имя атрибута в поле **Имя**. В этом примере в качестве имени утверждения используется **имя роли**.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

10. Чтобы протестировать в вашем приложении единый вход, инициированный поставщиком удостоверений, войдите в [панель доступа](https://myapps.microsoft.com) и выберите плитку приложения. Должен отобразиться токен SAML с полным списком ролей, назначенных пользователю, и с указанным вами именем утверждения.

## <a name="update-an-existing-role"></a>Обновление существующей роли

Чтобы обновить существующую роль, сделайте следующее:

1. Откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Войдите на сайт песочницы Graph с помощью учетных данных глобального администратора или соадминистратора вашего клиента.
    
3. Выберите **бета-версию** и получите от клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Если вы используете несколько каталогов, следуйте такому шаблону: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Диалоговое окно песочницы Graph с запросом на получение субъектов-служб](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. В полученном списке найдите субъект-службу, в который нужно внести изменения. Для поиска приложений в списке субъектов-служб можно также использовать клавиши CTRL+F. Найдите идентификатор объекта, скопированный со страницы **Свойства**, и используйте следующий запрос, чтобы получить соответствующий субъект-службу:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Запрос на получение субъекта-службы, который необходимо изменить](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Извлеките свойство **appRoles** из объекта субъекта-службы.
    
    ![Сведения о свойстве appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Чтобы обновить существующую роль, сделайте следующее:

    ![Текст запроса PATCH, в котором выделены атрибуты description и displayname](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. Измените метод **GET** на **PATCH**.

    Б. Скопируйте существующие роли и вставьте их в поле **Текст запроса**.

    c. Обновите значение роли, при необходимости указав новые значения описания роли, значения роли или отображаемого имени роли.

    d. Обновив все необходимые роли, нажмите кнопку **Выполнить запрос**.
        
## <a name="delete-an-existing-role"></a>Удаление существующей роли

Чтобы удалить существующую роль, сделайте следующее:

1. В другом окне откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Войдите на сайт песочницы Graph с помощью учетных данных глобального администратора или соадминистратора вашего клиента.

3. Выберите **бета-версию** и получите от клиента список субъектов-служб с помощью следующего запроса:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Если вы используете несколько каталогов, следуйте такому шаблону: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Диалоговое окно песочницы Graph с запросом на получение списка субъектов-служб](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. В полученном списке найдите субъект-службу, в который нужно внести изменения. Для поиска приложений в списке субъектов-служб можно также использовать клавиши CTRL+F. Найдите идентификатор объекта, скопированный со страницы **Свойства**, и используйте следующий запрос, чтобы получить соответствующий субъект-службу:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Запрос на получение субъекта-службы, который необходимо изменить](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Извлеките свойство **appRoles** из объекта субъекта-службы.
    
    ![Сведения о свойстве appRoles из объекта субъекта-службы.](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Чтобы удалить существующую роль, сделайте следующее:

    ![Текст запроса PATCH, в котором для IsEnabled задано значение false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Измените метод **GET** на **PATCH**.

    Б. Скопируйте существующие роли из приложения и вставьте их в поле **Текст запроса**.
        
    c. Присвойте значение **false** параметру **IsEnabled** для той роли, которую нужно удалить.

    d. Снова выберите **Выполнение запроса**.
    
    > [!NOTE] 
    > Убедитесь, что роль пользователя msiam_access создана и в ней правильно указан идентификатор.
    
7. Когда роль будет отключена, удалите этот блок роли из раздела **appRoles**. Оставьте в качестве метода **PATCH** и нажмите кнопку **Выполнить запрос**.
    
8. После запуска запроса роль будет удалена.
    
    > [!NOTE]
    > Прежде чем роль можно будет удалить, ее необходимо отключить. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные инструкции см. в [документации по приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
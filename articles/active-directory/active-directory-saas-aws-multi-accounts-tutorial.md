---
title: Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) для подключения нескольких учетных записей | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и несколькими учетными записями Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: 929caab0aafdad24062e372e458f16a5f36cce73
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Руководство по интеграции Azure Active Directory с несколькими учетными записями Amazon Web Services (AWS)

В этом руководстве описано, как интегрировать приложение Azure Active Directory (Azure AD) с несколькими учетными записями Amazon Web Services (AWS).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
- Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

- Подписка Azure AD уровня "Базовый" или "Премиум";
- несколько учетных записей Amazon Web Services с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Amazon Web Services из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции
Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Amazon Web Services (AWS)**, выберите **Amazon Web Services (AWS)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Amazon Web Services (AWS) в списке результатов](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Добавив приложение, перейдите к странице **Свойства** и скопируйте **идентификатор объекта**.

    ![Amazon Web Services (AWS) в списке результатов](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Amazon Web Services (AWS).

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Amazon Web Services (AWS)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Amazon Web Services (AWS)** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах для единого входа в приложение Amazon Web Services (AWS)](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка атрибута единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута  | Значение атрибута | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Роль            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Необходимо настроить подготовку пользователя в Azure AD для получения всех ролей из консоли AWS. Этапы подготовки см. ниже.

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Настройка атрибута единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. В текстовом поле **Пространство имен** введите значение пространства имен, показанное для этой строки.
    
    d. Нажмите кнопку **ОК**.

6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

9. Щелкните **AWS Home** (Домашняя страница AWS).
   
    ![Настройка главной страницы единого входа][11]

10. Щелкните **IAM**. (Управление удостоверениями и доступом). 
   
    ![Настройка удостоверения для единого входа][12]

11. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик). 
   
    ![Настройка поставщика единого входа][13]

12. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия. 
   
    ![Настройка диалогового окна единого входа][14]
 
    a. Выберите для параметра **Provider Type** (Тип поставщика) значение **SAML**.

    Б. В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).

    c. Чтобы отправить **файл метаданных**, скачанный с портала Azure, нажмите кнопку **Choose File** (Выбрать файл).

    d. Щелкните **Следующий шаг**.

13. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать). 
    
    ![Настройка проверки единого входа][15]

14. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create role** (Создать роль). 
    
    ![Настройка ролей для единого входа][16]

15. На странице **Create role** (Создание роли) выполните следующие действия.  
    
    ![Настройка доверия для единого входа][19] 

    a. В разделе **Select type of trusted entity** (Выберите тип доверенной сущности) выберите **SAML 2.0 federation** (Федерация SAML 2.0).

    Б. В разделе **Choose a SAML 2.0 Provider** (Выберите поставщик SAML 2.0) выберите **поставщик SAML**, созданный ранее (например, *WAAD*).

    c. Установите флажок **Allow programmatic and AWS Management Console access** (Разрешить программный доступ и доступ через консоль управления AWS).
  
    d. Щелкните **Next: Permissions** (Далее: разрешения).

16. В диалоговом окне **Attach Permissions Policies** (Привязка политик разрешений) щелкните **Next: Review** (Далее: проверка).  
    
    ![Настройка политики единого входа][33]

17. В диалоговом окне **Review** (Обзор) выполните следующие действия.   
    
    ![Проверка настройки единого входа][34] 

    a. В текстовом поле **Role name** (Имя роли) введите имя роли.

    Б. В текстовом поле **Role description** (Описание роли) введите описание.

    a. Щелкните **Создать роль**.

    Б. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

18. Выйдите из текущей учетной записи AWS и войдите в систему с другой учетной записью, в которой нужно настроить единый вход с помощью Azure AD.

19. Выполните шаги 9–17, чтобы создать несколько ролей, которые необходимо настроить для этой учетной записи. При наличии более двух учетных записей выполните те же шаги для всех учетных записей, чтобы создать для них роли.

20. После создания всех ролей в учетных записях они отображаются в соответствующем списке **ролей**.

    ![Настройка ролей](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Необходимо зафиксировать все ARN ролей и доверенные сущности для всех ролей в учетных записях, которые необходимо вручную сопоставлять с приложением Azure AD. 

22. Щелкните роли, чтобы скопировать значения **ARN роли** и **доверенных сущностей**. Эти значения нужны всем ролям, которые необходимо создать в Azure AD.

    ![Настройка ролей](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Выполните то же самое для всех ролей во всех учетных записях и сохраните их всех в формате **ARN роли,доверенные сущности** в блокноте. 

24. В другом окне откройте [обозреватель Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

    a. Войдите на сайт обозревателя Graph с учетными данными глобального администратора или соадминистратора вашего клиента.

    Б. У вас должно быть достаточно разрешений для создания ролей. Щелкните **Изменить разрешения**, чтобы получить требуемые разрешения. 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Выберите следующие разрешения из списка (если у вас их еще нет) и щелкните "Изменить разрешения". 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Вам необходимо снова войти в систему и принять запрос. После принятия запроса вы снова войдете в обозреватель Graph.

    д. В раскрывающемся списке измените версию на **Бета**. Чтобы получить все субъекты-службы клиента, используйте следующий запрос:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    При использовании нескольких каталогов можно использовать следующий шаблон, в котором есть основной домен: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. В полученном списке субъектов-служб найдите ту, в которую нужно внести изменения. Также для поиска приложений в списке субъектов-служб можно использовать сочетание клавиш CTRL+F. Используя **идентификатор объекта**, который был скопирован со страницы свойств Azure AD, выполните следующий запрос для получения соответствующего субъекта-службы.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    ж. Извлеките свойство appRoles из объекта субъекта-службы. 

    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Вы можете добавлять новые роли только после **msiam_access** для операции исправления. Вы можете добавить столько ролей, сколько нужно вашей организации. Azure Active Directory будет отправлять **значение** этих ролей в качестве значения утверждения в ответе SAML.
    
    j. Вернитесь к своему обозревателю Graph и измените метод с **GET** на **PATCH**. Примените к объекту субъекта-службы нужные роли, обновив свойство appRoles, как показано в примере выше. Щелкните **Выполнить запрос**, чтобы выполнить операцию исправления. Сообщение об успешном выполнении подтвердит создание роли для приложения Amazon Web Services

    ![Диалоговое окно обозревателя Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Добавив новые роли в субъект-службу, вы сможете назначить эти роли и группы пользователям. Для этого перейдите на портал и найдите приложение Amazon Web Services. Щелкните вкладку **Пользователи и группы** в верхней части окна. 

26. Мы рекомендуем создавать группы для каждой роли AWS, чтобы вы могли присвоить определенную роль этой группе. Обратите внимание, что группа сопоставляется с ролью по принципу "один к одному". Затем можно добавить участников, которые принадлежат к этой группе.

27. После создания групп выберите группу и присвойте ее приложению. 

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Чтобы присвоить роль группе, выберите роль и нажмите кнопку **Присвоить** в нижней части страницы.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Обратите внимание, что вам необходимо обновить сеанс на портале Azure, чтобы увидеть новые роли.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services на панели доступа, вы перейдете на страницу приложения Amazon Web Services с возможностью выбрать роль.

![Добавление атрибута для настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Также можно проверить ответ SAML, чтобы увидеть роли, передаваемые как утверждения.

![Добавление атрибута для настройки единого входа](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png


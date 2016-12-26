---
title: "Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) | Документация Майкрософт"
description: "Узнайте, как использовать Amazon Web Services (AWS) с Azure Active Directory для реализации единого входа, автоматической подготовки и многого другого."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3e4bf3c31c077893b54f5d752c8d525f56230070
ms.openlocfilehash: cc28e46b72a7e89c3a1d112e7176ee1afca99344


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Руководство по интеграции Azure Active Directory с Amazon Web Services
Цель этого руководства — показать, как интегрировать Amazon Web Services с Azure Active Directory (Azure AD).  
Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Amazon Web Services. 
* Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

* подписка Azure AD;
* подписка с поддержкой единого входа в Amazon Web Services.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление Amazon Web Services из коллекции. 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции
Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

### <a name="to-add-amazon-web-services-aws-from-the-gallery-perform-the-following-steps"></a>Чтобы добавить Amazon Web Services из коллекции, выполните следующие действия:
1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1] 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню. 
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** . 
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**. 
   
    ![Приложения][4]
6. В поле поиска введите **Amazon Web Services (AWS)**.
   
    ![Приложения][5]
7. В области результатов выберите **Amazon Web Services (AWS)** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Приложения][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD с Amazon Web Services с использованием тестового пользователя Britta Simon.

Для настройки единого входа Azure AD необходимо знать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Amazon Web Services.

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Amazon Web Services](#creating-a-halogen-software-test-user)** требуется для создания соответствующего пользователя Britta Simon в Amazon Web Services, связанного с ее представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Amazon Web Services.  
Приложение Amazon Web Services ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML**. На следующем снимке экрана приведен пример.

![Настройка единого входа][27]

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На странице интеграции с приложением **Amazon Web Services** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7]
2. На странице **Как пользователи должны входить в Amazon Web Services?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа][8]
3. На странице диалогового окна **Настроить параметры приложения** нажмите кнопку "Далее". 
   
    ![Настройка параметров приложения][9]
4. На странице **Настройка единого входа в Amazon Web Services** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.
   
    ![Настройка единого входа][10]
5. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.
6. Щелкните **Console Home**(Домашняя страница консоли).
   
    ![Настройка единого входа][11]
7. Щелкните **Identity and Access Management**(Управление удостоверениями и доступом). 
   
    ![Настройка единого входа][12]
8. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик). 
   
    ![Настройка единого входа][13]
9. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия. 
   
    ![Настройка единого входа][14]
   
     а. Выберите для параметра **Тип поставщика** значение **SAML**.
   
     b. В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).
   
     В. Чтобы отправить загруженный файл метаданных, нажмите кнопку **Выбрать файл**.
   
     d. Щелкните **Следующий шаг**.
10. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать). 
    
    ![Настройка единого входа][15]
11. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create New Role** (Создать новую роль). 
    
    ![Настройка единого входа][16]
12. В диалоговом окне **Set Role Name** (Настройка имени роли) выполните следующие действия. 
    
    ![Настройка единого входа][17]
    
    а. В текстовом поле **Имя роли** введите имя роли (например, *TestUser*).
    
    b. Щелкните **Следующий шаг**.
13. В диалоговом окне **Select Role Type** (Выбор типа роли) выполните следующие действия. 
    
    ![Настройка единого входа][18]
    
    а. Выберите **Роль для доступа поставщика удостоверений**.
    
    b. В разделе **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Предоставление единого входа через Интернет (WebSSO) для доступа к поставщикам SAML) щелкните **Select** (Выбрать).
14. В диалоговом окне **Establish Trust** (Установка доверия) выполните следующие действия.  
    
    ![Настройка единого входа][19]
    
     а. В качестве поставщика SAML выберите поставщик SAML, созданный ранее (например, *WAAD*). 
    
     b. Щелкните **Следующий шаг**.
15. В диалоговом окне **Verify Role Trust** (Проверка доверия роли) нажмите кнопку **Next Step** (Следующий шаг). 
    
    ![Настройка единого входа][32]
16. В диалоговом окне **Attach Policy** (Присоединение политики) нажмите кнопку **Next Step** (Следующий шаг).  
    
    ![Настройка единого входа][33]
17. В диалоговом окне **Review** (Обзор) выполните следующие действия.   
    
    ![Настройка единого входа][34]
    
     а. Скопируйте значение в поле **ARN роли** .
    
     b. Скопируйте значение ARN **Доверенные сущности** .
    
     c. Щелкните **Создать роль**. 
18. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![Что такое Azure AD Connect?][20]
19. На странице **Подтверждение единого входа** щелкните **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Что такое Azure AD Connect?][22]
20. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** . 
    
    ![Настройка единого входа][21]
21. Щелкните **добавить атрибут пользователя**. 
    
    ![Настройка единого входа][23]
22. В диалоговом окне "Добавить атрибут пользователя" выполните следующие действия. 
    
    ![Настройка единого входа][24] 
    
    а. В текстовом поле **Имя атрибута** введите **https://aws.amazon.com/SAML/Attributes/Role**.
    
    b. В текстовом поле **Значение атрибута** введите **[значение ARN роли],[значение ARN доверенной сущности]**.
    
    > [!TIP]
    > Это значения, скопированные из диалогового окна «Обзор» при создании роли. 
    > 
    > 
    
    В. Щелкните **Завершить**, чтобы закрыть диалоговое окно **Добавить атрибут пользователя**.
23. Щелкните **добавить атрибут пользователя**. 
    
    ![Настройка единого входа][23]
24. В диалоговом окне "Добавить атрибут пользователя" выполните следующие действия. 
    
    ![Настройка единого входа][25]

     а. В текстовом поле **Имя атрибута** введите **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. В текстовом поле **Значение атрибута** введите или выберите из раскрывающегося списка значение **user.userprincipalname**.

    ![Настройка единого входа][35]

     c. Щелкните **Завершить**, чтобы закрыть диалоговое окно **Добавить атрибут пользователя**.

1. Нажмите кнопку **Применить изменения**. 
   
   ![Настройка единого входа][26]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
    c. Нажмите кнопку Далее.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия: 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-amazon-web-services-aws-test-user"></a>Создание тестового пользователя Amazon Web Services
Цель этого раздела — создать пользователя с именем Britta Simon в Amazon Web Services.

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-services-aws-perform-the-following-steps"></a>Чтобы создать пользователя с именем Britta Simon в Amazon Web Services, выполните следующие действия:
1. Войдите на сайт компании **Amazon Web Services** в качестве администратора.
2. Щелкните значок **домашней страницы консоли** . 
   
    ![Настройка единого входа][11]
3. Щелкните Identity and Access Management (Управление удостоверениями и доступом). 
   
    ![Настройка единого входа][28]
4. На панели мониторинга щелкните "Пользователи", а затем — Create New Users (Создать новых пользователей). 
   
    ![Настройка единого входа][29]
5. В диалоговом окне Create User (Создание пользователя) выполните следующие действия. 
   
    ![Настройка единого входа][30]
   
     а. В текстовых полях **Введите имена пользователей** введите имя пользователя (userprincipalname) в Azure AD.
   
     b. Щелкните **Создать**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Amazon Web Services.

![Назначение пользователя][31]

**Чтобы назначить Britta Simon в CloudPassage, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][26]
2. В списке приложений выберите **Amazon Web Services**.
   
    ![Назначение пользователя][27]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][25]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][29]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку Amazon Web Services на панели доступа, вы автоматически войдете в приложение Amazon Web Services.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png

























<!--HONumber=Nov16_HO4-->



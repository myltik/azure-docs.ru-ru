<properties
    pageTitle="Руководство. Интеграция Azure Active Directory с приложением Icertis Contract Management Platform | Microsoft Azure"
    description="Узнайте, как настроить единый вход между Azure Active Directory и Icertis Contract Management Platform."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Руководство. Интеграция Azure Active Directory с приложением Icertis Contract Management Platform

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с Icertis Contract Management Platform.

Интеграция Icertis Contract Management Platform с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD можно контролировать доступ к Icertis Contract Management Platform.
- Вы можете включить автоматический вход пользователей в Icertis Contract Management Platform (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Icertis Contract Management Platform, вам потребуется следующее:

- подписка Azure AD;
- подписка Icertis Contract Management Platform с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Icertis Contract Management Platform из коллекции.
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Добавление Icertis Contract Management Platform из коллекции.
Чтобы настроить интеграцию Icertis Contract Management Platform с Azure AD, вам потребуется добавить Icertis Contract Management Platform из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Icertis Contract Management Platform из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
    
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Icertis Contract Management Platform**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_01.png)

7. В области результатов выберите **Icertis Contract Management Platform** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Выбор приложения в коллекции](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Icertis Contract Management Platform с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Icertis Contract Management Platform соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Icertis Contract Management Platform

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Icertis Contract Management Platform.

Чтобы настроить и проверить единый вход Azure AD в Icertis Contract Management Platform, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Icertis Contract Management Platform](#creating-a-icertis-contract-management-platform-test-user)** требуется для создания пользователя Britta Simon в Icertis Contract Management Platform, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале и настроить его в Icertis Contract Management Platform.

**Чтобы настроить единый вход Azure AD в Icertis Contract Management Platform, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Icertis Contract Management Platform** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Icertis Contract Management Platform?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_03.png) 

3. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_04.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.icertis.com`.

    b. В нижней части страницы нажмите кнопку **Далее**


    > [AZURE.NOTE] Обратите внимание, что значения, указанные выше, используются в качестве примера. Их необходимо заменить фактическим URL-адресом входа. Чтобы получить эти значения, обратитесь в службу поддержки Icertis Contract Management Platform.

4. На странице **Настройка единого входа в Icertis Contract Management Platform** выполните приведенные ниже действия, после чего нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_05.png)

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.

5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Icertis Contract Management Platform и предоставьте следующие сведения: 

    — **скачанный файл метаданных** ; 
    
    — **идентификатор сущности** 
        
    — **URL-адрес единого входа SAML** 
        
    — **URL-адрес службы единого выхода**

6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  

    ![единого входа Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.
    
![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-a-icertis-contract-management-platform-test-user"></a>Создание тестового пользователя Icertis Contract Management Platform

В этом разделе описано, как создать пользователя Britta Simon в Icertis Contract Management Platform. Чтобы добавить пользователей в Icertis Contract Management Platform, обратитесь в службу поддержки Icertis Contract Management Platform.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления ей доступа в Icertis Contract Management Platform.
    
![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Icertis Contract Management Platform, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Icertis Contract Management Platform**.

    ![Настройка единого входа](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
    
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]



### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Icertis Contract Management Platform на панели доступа, вы автоматически войдете в приложение Icertis Contract Management Platform.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->



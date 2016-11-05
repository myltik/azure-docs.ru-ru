---
title: Руководство. Интеграция Azure Active Directory с Mixpanel | Microsoft Docs
description: Сведения о настройке единого входа Azure Active Directory в Mixpanel.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-mixpanel"></a>Руководство. Интеграция Azure Active Directory с Mixpanel
Цель этого учебника — показать, как интегрировать Mixpanel с Azure Active Directory (Azure AD).

Интеграция Mixpanel с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Mixpanel.
* Вы можете включить автоматический вход пользователей в Mixpanel (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Mixpanel, вам потребуется:

* подписка Azure AD;
* Подписка с поддержкой единого входа Mixpanel

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Mixpanel из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-mixpanel-from-the-gallery"></a>Добавление Mixpanel из коллекции
Чтобы настроить интеграцию Mixpanel с Azure AD, необходимо добавить Mixpanel из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mixpanel из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Mixpanel**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_01.png)
7. В области результатов выберите **Mixpanel** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Mixpanel с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Mixpanel соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mixpanel.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mixpanel.

Чтобы настроить и проверить единый вход Azure AD в Mixpanel, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Mixpanel](#creating-a-mixpanel-test-user)** требуется для создания в Mixpanel пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Mixpanel.

**Чтобы настроить единый вход Azure AD в Mixpanel, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Mixpanel** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Mixpanel?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_04.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Mixpanel, по следующей схеме: **https://mixpanel.com/login/**.

    > [AZURE.NOTE] Зарегистрируйтесь на сайте [https://mixpanel.com/register/](https://mixpanel.com/register/), настройте учетные данные для входа в систему и обратитесь в [службу поддержки Mixpanel](mailto:support@Mixpanel.com), чтобы включить параметры единого входа для своего клиента. Вы также можете получить URL-адрес единого входа, обратившись в службу поддержки Mixpanel.

    b. Нажмите кнопку **Далее**.



1. На странице **Настройка единого входа в Mixpanel** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_05.png) 
   
    а. Щелкните **Загрузить сертификат**и сохраните файл сертификата на свой компьютер. 
   
    b. Нажмите кнопку **Далее**.
2. В отдельном окне браузера войдите в приложение Mixpanel под учетной записью администратора.
3. Щелкните маленький значок **шестеренки** в левом нижнем углу страницы. 
   
    ![Единый вход в Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) 
4. Откройте вкладку **Access security** (Безопасность доступа) и щелкните **Change settings** (Изменить параметры).
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) 
5. На странице диалогового окна **Change your certificate** (Изменение сертификата) нажмите кнопку **Choose file** (Выбрать файл), чтобы передать скачанный сертификат, а затем нажмите кнопку **Next** (Далее).
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) 
6. На странице диалогового окна **Настройка единого входа в Mixpanel** классического портала Azure скопируйте значение **URL-адрес службы единого входа**, вставьте его в текстовое поле URL-адреса аутентификации на странице диалогового окна **Изменение URL-адреса проверки подлинности** и нажмите кнопку **Далее**.
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) 
7. Нажмите кнопку **Done**(Готово).
8. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.
В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-mixpanel-test-user"></a>Создание тестового пользователя Mixpanel
Цель этого раздела — создать пользователя с именем Britta Simon в Mixpanel. 

1. Выполните вход на веб-сайт компании Mixpanel в качестве администратора.
2. Щелкните маленькую кнопку с шестеренкой в левом нижнем углу страницы, чтобы открыть окно **Параметры** .
3. Откройте вкладку **Команды** .
4. В текстовое поле **член команды** введите электронный адрес пользователя Britta Simon на портале Azure.
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) 
5. Нажмите кнопку **Пригласить**. 

Пользователь получит по электронной почте письмо с предложением настроить свой профиль.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Mixpanel.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mixpanel, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Mixpanel**.
   
    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Mixpanel на панели доступа, вы автоматически войдете в приложение Mixpanel.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->



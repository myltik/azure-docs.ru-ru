<properties
    pageTitle="Руководство. Интеграция Azure Active Directory с Lesson.ly | Microsoft Azure"
    description="Узнайте, как настроить единый вход Azure Active Directory в Lesson.ly."
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



# <a name="tutorial:-azure-active-directory-integration-with-lesson.ly"></a>Руководство по интеграции Azure Active Directory с Lesson.ly

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Lesson.ly.

Интеграция Lesson.ly с Azure AD дает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Lesson.ly.
- Вы можете включить автоматический вход пользователей в Lesson.ly (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Lesson.ly, вам потребуется:

- подписка Azure AD;
- подписка Lesson.ly с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Lesson.ly из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-lesson.ly-from-the-gallery"></a>Добавление Lesson.ly из коллекции
Чтобы настроить интеграцию Lesson.ly с Azure AD, необходимо добавить Lesson.ly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Lesson.ly из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
 
    ![Приложения][4]

6. В поле поиска введите **Lesson.ly**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)

7. В области результатов выберите **Lesson.ly** и щелкните **Завершить**, чтобы добавить приложение.


![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Lesson.ly с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Lesson.ly соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Lesson.ly.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Lesson.ly.

Чтобы настроить и проверить единый вход Azure AD в Lesson.ly, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Lesson.ly](#creating-a-Lessonly-test-user)** требуется для создания в Lesson.ly пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Lesson.ly со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

Приложение Lesson.ly предусматривает использование проверочных утверждений SAML в определенном формате, что предполагает добавление настраиваемых сопоставлений атрибутов в конфигурацию **атрибутов токена SAML** .
На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) 

**Чтобы настроить единый вход Azure AD в Lesson.ly, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением Lesson.ly в меню в верхней части страницы щелкните **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**.

    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) 

2. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) 

    а. Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.

    b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.

    d. В нижней части страницы нажмите кнопку **Завершить**

3. Нажмите кнопку **Применить изменения**.

4. В браузере нажмите кнопку **Назад** , чтобы снова открыть диалоговое окно "Быстрый запуск".

5. Щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6] 

6. На странице **Как пользователи должны входить в Lesson.ly?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) 

7. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
 
    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) 


    а. В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый пользователями для входа в приложение Lesson.ly, в таком формате: **https://companyname.Lesson.ly/signin**. Значение **companyname** в ссылке необходимо заменить фактическим именем.


8. На странице **Настройка единого входа в Lesson.ly** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) 

    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


9. Чтобы получить помощь по настройке единого входа для приложения, обратитесь в службу поддержки Lesson.ly, используя адрес dev@lesson.ly. Прикрепите к сообщению скачанный сертификат и укажите URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода), чтобы специалисты Lesson.ly смогли настроить единый вход со своей стороны.


10. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
  
    ![единого входа Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-a-lesson.ly-test-user"></a>Создание тестового пользователя Lesson.ly

Цель этого раздела — создать пользователя с именем Britta Simon в Lesson.ly. Приложение Lesson.ly поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Lesson.ly (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Lesson.ly.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Lesson.ly.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Lesson.ly, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Lesson.ly**.

    ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) 

1. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203] 

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]



### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Lesson.ly на панели доступа, вы автоматически войдете в приложение Lesson.ly.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->



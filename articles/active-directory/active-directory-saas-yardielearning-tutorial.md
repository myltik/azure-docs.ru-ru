<properties
    pageTitle="Учебник. Интеграция Azure Active Directory с Yardi eLearning | Microsoft Azure"
    description="Узнайте, как настроить единый вход Azure Active Directory в Yardi eLearning."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-yardi-elearning"></a>Учебник. Интеграция Azure Active Directory с Yardi eLearning

Цель этого руководства — показать, как интегрировать Yardi eLearning с Azure Active Directory (Azure AD).

Интеграция приложения Yardi eLearning с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ пользователей к Yardi eLearning.
- Вы можете включить автоматический вход пользователей в Yardi eLearning (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Yardi eLearning, вам потребуется:

- подписка Azure AD;
- подписка Yardi eLearning с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Yardi eLearning из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-yardi-elearning-from-the-gallery"></a>Добавление Yardi eLearning из коллекции
Чтобы настроить интеграцию Yardi eLearning с Azure AD, необходимо добавить Yardi eLearning из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Yardi eLearning из коллекции, выполните указанные далее действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Yardi eLearning**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_01.png)

7. В области результатов выберите **Yardi eLearning** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Yardi eLearning с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Yardi eLearning соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Yardi eLearning.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Yardi eLearning.

Чтобы настроить и проверить единый вход Azure AD в Yardi eLearning, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Yardi eLearning](#creating-a-yardi-elearning-test-user)** требуется для создания пользователя Britta Simon в Yardi eLearning, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Yardi eLearning.



**Чтобы настроить единый вход Azure AD в Yardi eLearning, выполните указанные далее действия.**

1. На классическом портале Azure на странице интеграции с приложением **Yardi eLearning** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Yardi eLearning?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 
    ![Настройка единого входа](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
 
    ![Настройка единого входа](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_04.png) 


    а. В текстовом поле "URL-адрес входа" введите URL-адрес,используемый пользователями для входа в приложение Yardi eLearning, по следующей схеме: **https://\<название_организации\>.yardielearning.com/login**.

    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в Yardi eLearning** выполните указанные далее действия.

    ![Настройка единого входа](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_05.png) 

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для приложения, отправьте в службу поддержки Yardi eLearning сообщение по электронному адресу [elearning@yardi.com](mailto:elearning@yardi.com) и вложите в него скачанный файл метаданных.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  

    ![единого входа Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.



![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yardielearning-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-a-yardi-elearning-test-user"></a>Создание тестового пользователя Yardi eLearning

Цель этого раздела — создать в приложении Yardi eLearning пользователя с именем Britta Simon. Приложение Yardi eLearning поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Yardi eLearning (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Если вам требуется вручную создать пользователя, необходимо обратиться в службу поддержки Yardi eLearning.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Yardi eLearning.

    ![Assign User][200] 

**Чтобы назначить пользователя Britta Simon приложению Yardi eLearning, выполните указанные далее действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
 
    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Yardi eLearning**.

    ![Настройка единого входа](./media/active-directory-saas-yardielearning-tutorial/tutorial_yardielearning_50.png) 

1. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203] 

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]



### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Yardi eLearning на панели доступа, вы автоматически войдете в приложение Yardi eLearning.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-yardielearning-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->



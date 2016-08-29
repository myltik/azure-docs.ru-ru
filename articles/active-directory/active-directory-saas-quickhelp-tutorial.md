.<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с QuickHelp | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и QuickHelp."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

.<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с QuickHelp

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением QuickHelp. Интеграция Azure AD с приложением QuickHelp обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к QuickHelp;
- вы можете включить автоматический вход пользователей в QuickHelp (единый вход) под учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с QuickHelp, вам потребуется:

- подписка Azure AD;
- подписка QuickHelp с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление QuickHelp из коллекции
2. Настройка и проверка единого входа в Azure AD.


## Добавление QuickHelp из коллекции
Чтобы настроить интеграцию QuickHelp с Azure AD, необходимо добавить QuickHelp из коллекции в список управляемых приложений SaaS.

**Чтобы добавить QuickHelp из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **QuickHelp**.

	![Приложения][5]

7. В области результатов выберите **QuickHelp** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Приложения][500]


##  Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в QuickHelp с использованием тестового пользователя Britta Simon.


Чтобы настроить и проверить единый вход Azure AD в QuickHelp, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя QuickHelp](#creating-a-quickhelp-test-user)** требуется для создания в QuickHelp пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение QuickHelp.


**Чтобы настроить единый вход Azure AD в QuickHelp, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **QuickHelp** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в QuickHelp?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][7]

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	.![Настройка параметров приложения][8]
 
     а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт QuickHelp (например, * https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Обратитесь в службу поддержки QuickHelp, если вы не знаете URL-адрес входа.

     b. Нажмите кнопку **Далее**.

 
4. На странице **Настройка единого входа в QuickHelp** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.

	![Что такое Azure AD Connect?][9]



1. Выполните вход на сайт компании QuickHelp в качестве администратора.

2. В верхнем меню щелкните **Администратор**.

	![Настройка единого входа][21]


1. В меню **Администратор QuickHelp** щелкните **Параметры**.

	![Настройка единого входа][22]

1. Щелкните **Authentication Settings** (Параметра аутентификации).

1. На странице **Параметры проверки подлинности** выполните следующие действия.

	![Настройка единого входа][23]

    а. Для параметра **SSO Type** (Тип SSO) выберите значение **WSFederation**.

    b. Чтобы передать скачанный файл метаданных Azure, нажмите кнопку **Browse** (Обзор), перейдите к файлу и щелкните **Upload Metadata** (Передать метаданные).

    c. В текстовом поле **Email** (Электронный адрес) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    г) В текстовом поле **First Name** (Имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    д. В текстовое поле **Last Name** (Фамилия) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    Е. На **панели действий** щелкните **Сохранить**.







6. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

	.![Что такое Azure AD Connect?][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	.![Что такое Azure AD Connect?][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png)
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png)
 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png)
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя QuickHelp

Цель этого раздела — создать пользователя с именем Britta Simon в QuickHelp. Для работы единого входа в Azure AD необходимо знать, какой пользователь в QuickHelp соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в QuickHelp.

QuickHelp поддерживает JIT-подготовку. Это означает, что при необходимости учетная запись пользователя автоматически создается в QuickHelp и связывается с учетной записью Azure AD.

В этом разделе никакие действия с вашей стороны не требуются.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к QuickHelp.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в QuickHelp, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **QuickHelp**.

	![Назначение пользователя][202]

1. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув плитку QuickHelp на панели доступа, вы автоматически войдете в приложение QuickHelp.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


.<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=AcomDC_0817_2016-->
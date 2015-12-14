<properties
	pageTitle="Руководство по интеграции Azure Active Directory с Namely | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Namely."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с Namely

Цель этого руководства — показать, как интегрировать Namely с Azure Active Directory (Azure AD).<br>Интеграция Namely с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Namely. 
- Вы можете включить автоматический вход пользователей в Namely (единый вход) с учетной записью Azure AD.
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Namely, вам потребуется:

- подписка Azure AD;
- подписка Namely с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Namely из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление Namely из коллекции
Чтобы настроить интеграцию Namely с Azure AD, необходимо добавить Namely из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Namely из коллекции, выполните следующие действия.**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Namely**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)<br>
7. В области результатов выберите **Namely** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)<br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Namely с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Namely соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Namely.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Namely.
 
Чтобы настроить и проверить единый вход Azure AD в Namely, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Namely](#creating-a-namely-test-user)** требуется для создания в Namely пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение Namely.




**Чтобы настроить единый вход Azure AD в Namely, выполните следующие действия.**

1. На портале Azure AD на странице интеграции с приложением **Namely** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Namely?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) <br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) <br>

    а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Namely (например, **https://fabrikam.Namely.com/*).

    b. Нажмите кнопку **Далее**.
 
 
4. На странице **Настройка единого входа в Namely** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) <br>

    а. Щелкните **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. В другом окне браузера войдите на корпоративный сайт Namely с правами администратора.

1. На панели инструментов в верхней части экрана щелкните **Company** (Компания). <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) <br>

1. Перейдите на вкладку **Settings** (Параметры). <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) <br>


1. Нажмите кнопку **SAML**. <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) <br>


1. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) <br>

    а. Щелкните **Enable SAML** (Включить SAML).

    b. На портале Azure на странице **Настройка единого входа в Namely** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Identity provider DDO URL** (URL-адрес DDO поставщика удостоверений).

    c. Откройте сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Identity provider certificate** (Сертификат поставщика удостоверений).

    г) Щелкните **Сохранить**.


6. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) <br>

5. В диалоговом окне на странице **Тип учетной записи пользователя** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  В диалоговом окне на странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. Из списка **Роль** выберите **Пользователь**. е. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) <br>
 
8. В диалоговом окне на странице **Получение временного пароля** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя Namely

Цель этого раздела — создать пользователя с именем Britta Simon в Namely.

**Чтобы создать пользователя с именем Britta Simon в Namely, выполните следующие действия.**

1. Войдите на корпоративный сайт Namely с правами администратора.

1. На панели инструментов в верхней части экрана щелкните **People** (Пользователи). <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) <br>

1. Щелкните вкладку **Directory** (Каталог). <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) <br>

1. Щелкните **Add New Person** (Добавить нового пользователя).



1. В диалоговом окне **Add New Person** (Добавление пользователя) выполните следующие действия.

    а. В текстовое поле **First Name** (Имя) введите **Britta**.

    b. В текстовое поле **Last Name** (Фамилия) введите **Simon**.

    c. В текстовое поле **Email** (Электронный адрес) введите электронный адрес пользователя Britta Simon на портале Azure.

    г) Щелкните **Сохранить**.





### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Namely. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Namely, выполните следующие действия.**

1. Чтобы открыть представление приложений, на портале Azure в представлении каталога в меню вверху щелкните **Приложения**. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **Namely**. <br><br>![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) <br>

1. В меню вверху щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Namely на панели доступа, вы автоматически войдете в приложение Namely.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1203_2015-->
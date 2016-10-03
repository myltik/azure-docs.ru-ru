<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Wizergos Productivity Software | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Wizergos Productivity Software."
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
	ms.date="09/15/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Wizergos Productivity Software 

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Wizergos Productivity Software.

Интеграция Wizergos Productivity Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Wizergos Productivity Software.
- Вы можете включить автоматический вход пользователей в Wizergos Productivity Software (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Wizergos Productivity Software, вам потребуется следующее:

- подписка Azure AD;
- подписка на Wizergos Productivity Software с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Wizergos Productivity Software из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление Wizergos Productivity Software из коллекции
Чтобы настроить интеграцию Wizergos Productivity Software с Azure AD, вам потребуется добавить Wizergos Productivity Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Wizergos Productivity Software из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
	
	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.
	
	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Wizergos Productivity Software**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)

7. В области результатов выберите **Wizergos Productivity Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Выбор приложения в коллекции](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Wizergos Productivity Software с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Wizergos Productivity Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Wizergos Productivity Software.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Wizergos Productivity Software.

Чтобы настроить и проверить единый вход в Azure AD в Wizergos Productivity Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Wizergos Productivity Software](#creating-a-wizergos-productivity-software-test-user)** требуется для создания пользователя Britta Simon в Wizergos Productivity Software, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Wizergos Productivity Software.

**Чтобы настроить единый вход в Azure AD с помощью Wizergos Productivity Software, сделайте следующее:**

1. На странице интеграции с приложением **Wizergos Productivity Software** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Wizergos Productivity Software?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
    
	![Настройка единого входа](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)

3. Если вы хотите настроить приложение в **режиме, инициированном IDP**, на странице с диалоговым окном **Настроить параметры приложения** выполните указанные ниже действия и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)

	а. В текстовом поле **Идентификатор** введите `https://www.wizergos.net`.

    b. В текстовом поле **URL-адрес ответа** введите: `https://www.wizergos.net/register_users/saml2/`.

	в) Нажмите кнопку **Далее**.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)

	а. В текстовом поле **URL-адрес входа** введите `https://www.wizergos.net`.

	b. Нажмите кнопку **Далее**.

5. На странице **Настройка единого входа в Wizergos Productivity Software** выполните указанные ниже действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_06.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.

6. В другом окне веб-браузера войдите на веб-сайт Wizergos Productivity Software своей компании в качестве администратора.

7. В меню с тремя полосками выберите **Администратор**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

8. На странице администрирования в меню слева выберите **Проверка подлинности** и нажмите кнопку **Azure AD**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

9. В разделе **Проверка подлинности** выполните следующие действия.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)

	а. Откройте скачанный сертификат из Azure AD и скопируйте отпечаток сертификата. Затем вставьте его в текстовое поле **Отпечаток сертификата**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_004.png)

	b. В текстовое поле **URL-адрес издателя** вставьте **URL-адрес издателя** из мастера настройки приложения в Azure AD.

	в) В текстовом поле **URL-адрес единого входа** введите значение **URL-адрес службы единого входа** из мастера настройки приложения Azure AD.

	г) В текстовом поле **URL-адрес единого выхода** введите значение **URL-адрес службы единого выхода** из мастера настройки приложения Azure AD.

	д. Нажмите кнопку **Сохранить**.

	> [AZURE.NOTE] Обратите внимание, что отпечаток сертификата не должен содержать пробелы.

7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
	![Единый вход в Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
	![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Wizergos Productivity Software

В этом разделе описано, как создать пользователя Britta Simon в приложении Wizergos Productivity Software. Обратитесь в службу поддержки Wizergos Productivity Software по адресу [support@wizergos.com](emailTo:support@wizergos.com), чтобы добавить пользователей на платформу Wizergos Productivity Software.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к Wizergos Productivity Software.
	
   ![Назначение пользователя][200]

**Чтобы назначить Britta Simon в Wizergos Productivity Software, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
    
	![Назначение пользователя][201]

2. В списке приложений выберите **Wizergos Productivity Software**.
    
	![Настройка единого входа](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.
    
	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.
    
	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку Wizergos Productivity Software на панели доступа, вы автоматически войдете в приложение Wizergos Productivity Software.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
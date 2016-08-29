.<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с ImageRelay | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в ImageRelay."
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
	ms.date="08/15/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с ImageRelay

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением ImageRelay. Интеграция ImageRelay с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ImageRelay.
- Вы можете включить автоматический вход пользователей в ImageRelay (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с ImageRelay, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа ImageRelay.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление ImageRelay из коллекции

2. Настройка и проверка единого входа в Azure AD.


## Добавление ImageRelay из коллекции
Чтобы настроить интеграцию ImageRelay с Azure AD, необходимо добавить ImageRelay из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ImageRelay из коллекции, выполните следующие действия.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	.![Приложения][4]

6. В поле поиска введите **ImageRelay**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. В области результатов выберите **ImageRelay** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в ImageRelay с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходима учетная запись пользователя, представляющая соответствующего пользователя в ImageRelay. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ImageRelay. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ImageRelay.

Чтобы настроить и проверить единый вход Azure AD в ImageRelay, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию;
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon;
4. **[Создание тестового пользователя ImageRelay](#creating-a-userecho-test-user)** требуется для создания в ImageRelay пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение ImageRelay.


**Чтобы настроить единый вход Azure AD в ImageRelay, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **ImageRelay** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

     .![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в ImageRelay?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    .![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

     .![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png)

    а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение ImageRelay (например, *https://fabrikam.ImageRelay.com/*).

    b. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в ImageRelay** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.

5. В другом окне браузера войдите на корпоративный сайт ImageRelay с правами администратора.

1. На панели инструментов вверху экрана выберите рабочую нагрузку **Users & Permissions** (Пользователи и разрешения).

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png)

1. Щелкните **Создать новое разрешение**.

	![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

1. В рабочей нагрузке **Параметры единого входа** установите флажок **This Group can only sign-in via Single Sign On** (Эта группа может входить только через единый вход) и нажмите кнопку **Сохранить**.

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png)

1. Откройте **Параметры учетной записи**.

	![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png)

1. Выберите рабочую нагрузку **Параметры единого входа**.

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

	а. На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес для входа**.


	b. На классическом портале Azure скопируйте **URL-адрес службы единого выхода** и вставьте его в текстовое поле **URL-адрес выхода**.

	c. В поле **Name Id Format** (Формат идентификатора имени) выберите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

	
    г) В разделе **Binding Options for Requests from the Service Provider (ImageRelay)** (Обязательные параметры для запросов от поставщика услуг (ImageRelay)) выберите **POST Binding** (Привязка POST).
   

	д. В разделе **509 Certificate** (Сертификат x.509) щелкните **Update Certificate** (Обновить сертификат).

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    Е. Откройте скачанный сертификат в блокноте, а затем скопируйте и вставьте его содержимое в текстовое поле X.509 Certificate (Сертификат X.509).
  
	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. В разделе **Just-In-Time User Provisioning** (JIT-подготовка пользователей) выберите **Enable Just-In-Time User Provisioning** (Включить JIT-подготовку пользователей).

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Выберите группу разрешений (например, **SSO Basic** (Базовый единый вход)), которой будет разрешено выполнять вход только через службу единого входа.

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Щелкните **Сохранить**.

6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

    .![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

    .![Единый вход в Azure AD][11]


### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя ImageRelay

Цель этого раздела — создать пользователя с именем Britta Simon в ImageRelay.

**Чтобы создать пользователя с именем Britta Simon в ImageRelay, выполните следующие действия.**

1. Войдите на корпоративный сайт ImageRelay с правами администратора.

1. Откройте раздел **Users & Permissions** (Пользователи и разрешения) и выберите параметр **Create SSO User** (Создать пользователя единого входа).

	.![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png)

1. Введите **адрес электронной почты**, **имя**, **фамилию** и **компанию** пользователя, которого нужно подготовить, и выберите группу разрешений (например, SSO Basic (Базовый единый вход)), т. е. группу, которая сможет выполнять вход только через службу единого входа.

	![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png)

1. Щелкните **Создать**.

### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ImageRelay.

.![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в ImageRelay, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните пункт **Приложения** в меню вверху.

	.![Назначение пользователя][201]

2. В списке приложений выберите **ImageRelay**.

	![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]


### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув элемент ImageRelay на панели доступа, вы автоматически войдете в приложение ImageRelay.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->
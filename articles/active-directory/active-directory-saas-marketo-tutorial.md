<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Marketo | Microsoft Azure"
	description="Узнайте, как настроить единый вход в Marketo через Azure Active Directory."
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
	ms.date="09/07/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Marketo

В этом руководстве описано, как интегрировать Marketo с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Marketo обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Marketo.
- Вы можете включить автоматический вход пользователей в Marketo (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Marketo, вам потребуется:

- подписка Azure AD;
- подписка Marketo с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. добавление Marketo из коллекции,
2. настройка и проверка единого входа в Azure AD.


## Добавление Marketo из коллекции
Чтобы настроить интеграцию Marketo с Azure AD, необходимо добавить Marketo из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Marketo из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Marketo**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)

7. В области результатов выберите **Marketo** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Marketo с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какому пользователю в Azure AD соответствует пользователь в Marketo. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Marketo.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Marketo.

Чтобы настроить и проверить единый вход Azure AD в Marketo, вам нужно выполнить следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Marketo](#creating-a-predictix-price-reporting-test-user)** требуется для создания в Marketo пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Marketo.


**Чтобы настроить единый вход Azure AD в Marketo, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Marketo** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Marketo?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://saml.marketo.com/sp`.
	
	b. В текстовое поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://login.marketo.com/saml/assertion/<munchkinid>`.

	3\. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Marketo** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы получить идентификатор Munchkin для приложения, войдите в Marketo, используя учетные данные администратора, и выполните следующие действия.

	а. Войдите в приложение Marketo, используя учетные данные администратора.

	b. Нажмите кнопку Admin (Администратор) в области навигации вверху.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. Перейдите в меню Integration (Интеграция) и щелкните ссылку Munchkin.
	
	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)

	г) Скопируйте идентификатор Munchkin, показанный на экране, и введите URL-адрес ответа в мастере настройки Azure AD.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)

6.	Для настройки единого входа в приложение выполните следующие действия.

	а. Войдите в приложение Marketo, используя учетные данные администратора.

	b. Нажмите кнопку Admin (Администратор) в области навигации вверху.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. Перейдите в меню Integration (Интеграция) и щелкните ссылку Single Sign On (Единый вход).

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png)

	г) Чтобы включить параметры SAML, нажмите кнопку Edit (Редактировать).
	
	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png)

	д. **Включите** параметры единого входа.

	Е. Введите идентификатор издателя, который вы скопировали в мастере настройки Azure AD.

	g. В текстовое поле Entity ID (Идентификатор сущности) введите URL-адрес в формате **http://saml.marketo.com/sp**.

	h. Укажите для параметра User ID Location (Расположение идентификатора пользователя) значение **в элементе идентификатора имени**.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)

	> [AZURE.NOTE] Если идентификатор пользователя имеет значение, отличное от имени участника-пользователя, измените это значение на вкладке Attribute (Атрибут).
	 
	i. Отправьте сертификат, загруженный из мастера настройки Azure AD. Сохраните параметры.

	j. Измените параметры перенаправления страниц.

	k. Скопируйте URL-адрес входа в мастере настройки Azure AD и вставьте в текстовое поле **URL-адрес для входа**.

	l. Скопируйте URL-адрес выхода в мастере настройки Azure AD и вставьте в текстовое поле **URL-адрес для выхода**.

	m. В поле URL-адреса ошибки вставьте URL-адрес экземпляра Marketo и сохраните параметры.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Чтобы включить единый вход для пользователей, выполните следующие действия.

	а. Войдите в приложение Marketo, используя учетные данные администратора.

	b. Нажмите кнопку **Admin** (Администратор) в области навигации вверху.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

	c. В меню **Security** (Безопасность) щелкните **Login Settings** (Параметры входа).

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)

	г) Проверьте значение **Require SSO** (Требуется единый вход) и сохраните параметры.
	
	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

8. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	![Единый вход в Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
 
	![Единый вход в Azure AD][11]


### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Marketo

В этом разделе описано, как создать пользователя Britta Simon в приложении Marketo. Выполните следующие шаги, чтобы создать пользователя платформы Marketo.

1. Войдите в приложение Marketo, используя учетные данные администратора.

2. Нажмите кнопку **Admin** (Администратор) в области навигации вверху.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png)

3. В меню **Security** (Безопасность) щелкните **Users & Roles** (Пользователи и роли).

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)

4. На вкладке Users (Пользователи) щелкните ссылку **Invite New User** (Пригласить нового пользователя).

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png)

5. Введите следующую информацию в мастере приглашения нового пользователя.

	а. Введите адрес **электронной почты** пользователя в соответствующее текстовое поле.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)

	b. Введите **имя**.
	
	c. Введите **фамилию**.

	г) Нажмите кнопку Next (Далее).

6. На вкладке **Permissions** (Разрешения) выберите роли пользователя и нажмите кнопку Next (Далее).

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)

7. Нажмите кнопку Send (Отправить), чтобы отправить приглашение пользователю.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Пользователь получит уведомление по электронной почте. Ему нужно будет перейти по ссылке, чтобы изменить пароль и активировать учетную запись.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Marketo.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Marketo, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	![Назначение пользователя][201]

2. В списке приложений выберите **Marketo**.

	![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Marketo на панели доступа, вы автоматически войдете в приложение Marketo.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->
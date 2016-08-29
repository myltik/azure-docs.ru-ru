.<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Predictix Ordering | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Predictix Ordering."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Predictix Ordering

В этом руководстве описано, как интегрировать Predictix Ordering с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Predictix Ordering обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Predictix Ordering.
- Вы можете включить автоматический вход пользователей в Predictix Ordering (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Predictix Ordering, вам потребуется:

- подписка Azure AD;
- подписка Predictix Ordering с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения Predictix Ordering из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление приложения Predictix Ordering из коллекции
Чтобы настроить интеграцию Predictix Ordering с Azure AD, необходимо добавить приложение Predictix Ordering из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Predictix Ordering из коллекции, выполните указанные ниже действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	.![Приложения][4]

6. В поле поиска введите **Predictix Ordering**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_01.png)

7. В области результатов выберите **Predictix Ordering** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_02.png)


##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Predictix Ordering с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Predictix Ordering соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Predictix Ordering.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Predictix Ordering.

Чтобы настроить и проверить единый вход Azure AD в Predictix Ordering, выполните следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Predictix Ordering](#creating-a-predictix-price-reporting-test-user)** требуется для создания в Predictix Ordering пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Predictix Ordering.


**Чтобы настроить единый вход Azure AD в Predictix Ordering, выполните указанные ниже действия.**

1. На классическом портале на странице интеграции с приложением **Predictix Ordering** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Predictix Ordering?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	.![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_04.png)

    а. В текстовое поле **URL-адрес входа** введите URL-адрес, применяемый пользователями для входа в приложение Predictix Ordering, в следующем формате: **https://\<имя\_компании-ценовая\_категория>.ordering.predictix.com/sso/request**.
	
	b. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Predictix Ordering** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Для настройки единого входа в приложении обратитесь в службу поддержки Predictix Ordering и предоставьте следующие сведения:

	• скачанный сертификат;

	• **идентификатор сущности**;

	• **URL-адрес единого входа SAML**;

	• **URL-адрес службы единого выхода**.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	.![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
 
	.![Единый вход в Azure AD][11]


### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


.![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя в Predictix Ordering

В этом разделе описано, как создать пользователя Britta Simon в приложении Predictix Ordering. Чтобы добавить пользователей в Predictix Ordering, обратитесь в службу поддержки Predictix Ordering.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Predictix Ordering.

.![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению Predictix Ordering, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	.![Назначение пользователя][201]

2. В списке приложений выберите **Predictix Ordering**.

	![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Predictix Ordering на панели доступа, вы автоматически войдете в приложение Predictix Ordering.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


.<!--Image references-->

[1]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->
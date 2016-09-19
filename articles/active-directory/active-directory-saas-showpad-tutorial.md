<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Showpad | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Showpad."
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Showpad

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Showpad.

Интеграция Azure AD с приложением Showpad обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Showpad.
- Вы можете включить автоматический вход пользователей в Showpad (единый вход) с использованием учетной записи Azure AD.
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Showpad, вам потребуется:

- подписка Azure AD;
- подписка Showpad.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Showpad из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Showpad из коллекции
Чтобы настроить интеграцию Showpad с Azure AD, необходимо добавить Showpad из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Showpad из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Приложения][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
 
	![Приложения][4]

6. В поле поиска введите **Showpad**.

	![Приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_01.png)

7. В области результатов выберите **Showpad** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_02.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Showpad с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Showpad соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Showpad.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Showpad.

Чтобы настроить и проверить единый вход Azure AD в Showpad, выполните описанные ниже процедуры.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Showpad](#creating-a-showpad-test-user)** требуется для создания пользователя Britta Simon в Showpad, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure и как настроить единый вход в приложении Showpad.



**Чтобы настроить единый вход Azure AD в Showpad, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Showpad** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Showpad?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_03.png)

3. На странице диалогового окна **Настроить параметры приложения** выполните описанные ниже действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_04.png)


    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Showpad, по следующей схеме: `https://<company name>.showpad.biz/login`.

	b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.showpad.biz`.

	c. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в приложение Showpad** выполните описанные ниже действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Войдите в клиент Showpad как администратор.

6. В верхнем меню щелкните пункт **Параметры**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_001.png)

7. Перейдите к параметру **Единый вход** и нажмите кнопку **Включить**.
 
	![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_002.png)

8. В диалоговом окне **Add a SAML 2.0 Service** (Добавление службы SAML 2.0) выполните следующие действия.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_003.png)

	а. В текстовом поле **Имя** введите имя поставщика идентификаторов (например, имя вашей компании).

	b. В качестве **источника метаданных** выберите **XML**.

	c. Скопируйте содержимое скачанного XML-файла с метаданными и вставьте его в текстовое поле **Metadata XML** (Метаданные XML).

	г) Выберите параметр **Auto-provision accounts for new users when they log in** (Автоматическая подготовка учетных записей для новых пользователей при первом входе).

	д. Нажмите кнопку **Submit** (Отправить).


10. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	.![Единый вход в Azure AD][10]


11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  
	.![Единый вход в Azure AD][11]






### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать в Azure AD тестового пользователя Showpad, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_05.png)

    а. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    b. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) Для параметра **Роль** выберите значение **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.


### Создание тестового пользователя Showpad

Цель этого раздела — создать в Showpad пользователя с именем Britta Simon.

Приложение Showpad поддерживает JIT-подготовку. Вы уже включили эту функцию на этапе **[настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)**.

В этом разделе никакие действия с вашей стороны не требуются.




### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Showpad.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Showpad, выполните следующие действия.**

1. На классическом портале Azure щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **Showpad**.

	![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]




### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент **Showpad** на панели доступа, вы автоматически войдете в приложение Showpad.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->
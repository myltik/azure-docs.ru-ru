<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с RightScale | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и RightScale."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с RightScale

Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением RightScale. Интеграция RightScale с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к RightScale.
- Вы можете включить автоматический вход пользователей в RightScale (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением RightScale, вам потребуется:

- подписка Azure AD;
- подписка RightScale с активированной функцией единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление RightScale из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление RightScale из коллекции
Чтобы настроить интеграцию RightScale с Azure AD, необходимо добавить RightScale из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RightScale из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
 
	![Приложения][4]

6. В поле поиска введите **RightScale**.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. В области результатов выберите **RightScale** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Выбор приложения в коллекции](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_02.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в RightScale с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в RightScale соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RightScale.


Чтобы настроить и проверить единый вход Azure AD в RightScale, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя RightScale](#creating-a-rightscale-test-user)** требуется для создания пользователя Britta Simon в RightScale, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить единый вход в приложении RightScale.



**Чтобы настроить единый вход Azure AD в RightScale, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **RightScale** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в RightScale?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png)

3. Если вы хотите настроить приложение в **режиме, инициированном IDP**, на странице с диалоговым окном **Настроить параметры приложения** выполните указанные ниже действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png)


    а. В текстовом поле "URL-адрес ответа" введите URL-адрес в следующем формате: `https://login.rightscale.com/login/saml2/consume`.

	b. Нажмите кнопку **Далее**.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
 
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png)

	а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение RightScale, применяя следующий шаблон: `https://login.rightscale.com/`.

	b. Нажмите кнопку **Далее**.

5. На странице **Настройка единого входа в RightScale** выполните указанные ниже действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png)

    а. Нажмите **Скачать сертификат** и сохраните файл сертификата в кодировке Base 64 на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить для приложения единый вход, нужно войти в клиент приложения RightScale с правами администратора.

	а. В меню в верхней части откройте вкладку **Параметры** и выберите **Единый вход**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png)

	b. Нажмите кнопку "**новый**" и добавьте **свои поставщики удостоверений SAML**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)

	в) В текстовом поле **Отображаемое имя** введите название своей компании.
	
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)

	г) Выберите **Allow RightScale-initiated SSO using a discovery hint** (Разрешить единый вход, инициированный RightScale, с использованием подсказки обнаружения) и в приведенном ниже текстовом поле введите **имя домена**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

	д. Скопируйте URL-адрес единого входа SAML из Azure AD в поле **SAML SSO Endpoint** (Конечная точка единого входа SAML) в RightScale.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
	
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

	Е. Скопируйте идентификатор сущности из Azure AD в поле **SAML EntityID** (идентификатор сущности SAML) в RightScale.
	
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
	
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

	g. Нажмите кнопку **Браузер**, чтобы отправить сертификат, скачанный в шаге 4.
	
	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

	h. Щелкните **Сохранить**.
	


6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  
	![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png)

    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    в) Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя RightScale

В этом разделе описано, как создать пользователя Britta Simon в приложении RightScale. Обратитесь в службу поддержки RightScale по адресу support@rightscale.com, чтобы добавить пользователей в платформу RightScale.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к RightScale.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в RightScale, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **RightScale**.

	![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув элемент RightScale на панели доступа, вы автоматически войдете в приложение RightScale.
	

## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
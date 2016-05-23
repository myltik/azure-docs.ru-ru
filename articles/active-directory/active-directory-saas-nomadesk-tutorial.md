<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Nomadesk | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Nomadesk."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Nomadesk

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Nomadesk.

Интеграция Azure AD с приложением Nomadesk обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Nomadesk.
- Вы можете включить автоматический вход пользователей в Nomadesk (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Nomadesk, вам потребуется:

- подписка Azure AD;
- подписка Nomadesk с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Nomadesk из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Nomadesk из коллекции
Чтобы настроить интеграцию Nomadesk с Azure AD, необходимо добавить Nomadesk из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Nomadesk из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Nomadesk**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_01.png)

7. В области результатов выберите **Nomadesk** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_02.png)

##  Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Nomadesk с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Nomadesk соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nomadesk.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Nomadesk.

Чтобы настроить и проверить единый вход Azure AD в Nomadesk, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Nomadesk](#creating-a-nomadesk-test-user)** требуется для создания в Nomadesk пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Nomadesk.



**Чтобы настроить единый вход Azure AD в Nomadesk, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Nomadesk** щелкните **Настройка единого входа**. Откроется диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Nomadesk?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_04.png)

	а. В текстовое поле "URL-адрес входа" введите URL-адрес, используемый для входа в приложение Nomadesk, в формате **https://mynomadesk.com/logon/saml/TENANTID**. Значение **TENANTID** необходимо заменить фактическим идентификатором клиента.

4. На странице **Настройка единого входа в Nomadesk** сделайте следующее.
 
	![Настройка единого входа](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Nomadesk по адресу [mailto:support@nomadesk.com](support@nomadesk.com). Вложите в сообщение скачанный файл сертификата и укажите URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода), чтобы специалисты Nomadesk смогли настроить единый вход со своей стороны.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_07.png)


8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Nomadesk

В этом разделе описано, как создать пользователя с именем Britta Simon в Nomadesk. Приложение Nomadesk поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к Nomadesk будет создан пользователь (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Если вам нужно создать пользователя вручную, обратитесь в службу поддержки Nomadesk.


### Назначение тестового пользователя Azure AD

Цель этого раздела — предоставить пользователю Britta Simon доступ к Nomadesk, чтобы пользователь мог использовать единый вход Azure.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Nomadesk, сделайте следующее:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
 
	![Назначение пользователя][201]

2. В списке приложений выберите **Nomadesk**.

	![Настройка единого входа](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Nomadesk на панели доступа, вы автоматически войдете в приложение Nomadesk.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0511_2016-->
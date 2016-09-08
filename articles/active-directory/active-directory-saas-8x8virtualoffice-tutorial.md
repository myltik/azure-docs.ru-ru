<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с 8x8 Virtual Office | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и 8x8 Virtual Office."
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
	ms.date="07/21/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с 8x8 Virtual Office

Цель этого руководства — показать, как интегрировать приложение 8x8 Virtual Office с Azure Active Directory (Azure AD).

Интеграция 8x8 Virtual Office с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к 8x8 Virtual Office.
- Вы можете включить автоматический вход пользователей в 8x8 Virtual Office (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с 8x8 Virtual Office, вам потребуется:

- подписка Azure AD;
- подписка на 8x8 Virtual Office с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого руководства — показать, как выполнить проверку единого входа Microsoft Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление 8x8 Virtual Office из коллекции.
2. Настройка и проверка единого входа в Microsoft Azure AD


## Добавление 8x8 Virtual Office из коллекции.
Чтобы настроить интеграцию приложения 8x8 Virtual Office с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 8x8 Virtual Office из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
	
	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **8x8 Virtual Office**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. В области результатов выберите **8x8 Virtual Office** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Выбор приложения в коллекции](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  Настройка и проверка единого входа в Microsoft Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Microsoft Azure AD в 8x8 Virtual Office с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в 8x8 Virtual Office соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 8x8 Virtual Office.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в 8x8 Virtual Office.

Чтобы настроить и проверить единый вход Microsoft Azure AD в 8x8 Virtual Office, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Microsoft Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** требуется для создания пользователя Britta Simon в 8x8 Virtual Office, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Microsoft Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа Microsoft Azure AD

В этом разделе описано, как включить единый вход Microsoft Azure AD на классическом портале и настроить его в приложении 8x8 Virtual Office.

**Чтобы настроить единый вход Microsoft Azure AD в 8x8 Virtual Office, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **8x8 Virtual Office** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **How would you like users to sign on to 8x8 Virtual Office** (Как пользователи должны входить в 8x8 Virtual Office?) выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png)

3. На диалоговой странице **Configure App Settings** (Настройка параметров приложения) выполните следующие действия, а затем нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    а. В текстовом поле **URL-адрес ответа** введите: `https://sso.8x8.com/saml2`.

	b. Нажмите кнопку **Далее**.

4. На странице **Configure single sign-on at 8x8 Virtual Office** (Настройка единого входа в 8x8 Virtual Office) выполните следующие действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.

5. Войдите в клиент 8x8 Virtual Office от имени администратора.
6. На панели приложения выберите **Virtual Office Account Mgr** (Диспетчер учетных записей Virtual Office).

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Выберите в качестве учетной записи, которой нужно управлять, **Business** (Рабочая) и нажмите кнопку **Sign In** (Вход).

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. В списке меню щелкните вкладку **Accounts** (Учетные записи).

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. В списке Accounts (Учетные записи) щелкните **Single Sign On** (Единый вход).

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. В разделе методов проверки подлинности установите флажок **Signle Sign On** (Единый вход), а затем установите переключатель **SAML**.

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Скопируйте значения параметров **URL-адрес единого входа SAML**, **Single Sing Out Service URL** (URL-адрес службы единого выхода) и **URL-адрес издателя** из Azure AD и вставьте их в качестве значений параметров **Sign In URL** (URL-адрес входа), **Sign Out URL** (URL-адрес выхода) и **Issuer URL** (URL-адрес издателя) в приложении 8x8 Virtual Office.

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

	![Настройка на стороне приложения](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Нажмите кнопку **Browse** (Обзор), чтобы отправить сертификат, скачанный из Azure AD.

13. Нажмите кнопку **Сохранить**.

14. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

15. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.
	
![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя 8x8 Virtual Office

Цель этого раздела — создать пользователя с именем Britta Simon в приложении 8x8 Virtual Office. Приложение 8x8 Virtual Office поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению 8x8 Virtual Office (если он еще не создан).

> [AZURE.NOTE] Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки 8x8 Virtual Office.


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к 8x8 Virtual Office.
	
![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в 8x8 Virtual Office, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	![Назначение пользователя][201]

2. В списке приложений выберите **8x8 Virtual Office**.

	![Настройка единого входа](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
	
	![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Microsoft Azure AD с помощью панели доступа.

Щелкнув элемент 8x8 Virtual Office на панели доступа, вы автоматически войдете в приложение 8x8 Virtual Office.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0824_2016-->
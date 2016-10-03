<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Cezanne HR Software | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Cezanne HR Software."
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


# Руководство. Интеграция Azure Active Directory с Cezanne HR Software

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Cezanne HR Software.

Интеграция Cezanne HR Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Cezanne HR Software.
- Вы можете включить автоматический вход пользователей в Cezanne HR Software (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Cezanne HR Software, вам потребуется следующее:

- подписка Azure AD;
- подписка на Cezanne HR Software с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Cezanne HR Software из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление Cezanne HR Software из коллекции
Чтобы настроить интеграцию Cezanne HR Software с Azure AD, вам потребуется добавить Cezanne HR Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cezanne HR Software из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
	
	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.
	
	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Cezanne HR Software**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. В области результатов выберите **Cezanne HR Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Выбор приложения в коллекции](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в Cezanne HR Software с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Cezanne HR Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cezanne HR Software.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Cezanne HR Software.

Чтобы настроить и проверить единый вход в Azure AD в Cezanne HR Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** требуется для создания пользователя Britta Simon в Cezanne HR Software, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Cezanne HR Software.

**Чтобы настроить единый вход Azure AD в Cezanne HR Software, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Cezanne HR Software** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Cezanne HR Software?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
    
	![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. На диалоговой странице **Configure App Settings** (Настройка параметров приложения) выполните следующие действия, а затем нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

	а. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. В текстовом поле **Идентификатор** введите `https://w3.cezanneondemand.com/CezanneOnDemand/`.

	в) В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

	г) Нажмите кнопку **Далее**.

	> [AZURE.NOTE] Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в службу поддержки Cezanne HR Software по адресу <mailto:info@cezannehr.com>.

4. На странице **Настройка единого входа в Cezanne HR Software** выполните следующие действия и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.

5. В другом окне веб-браузера войдите на веб-сайт Cezanne HR Software своей компании в качестве администратора.

6. В левой панели навигации щелкните **System Setup** (Настройка системы). Выберите пункт **Параметры безопасности**. Далее перейдите к пункту **Конфигурация единого входа**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. На панели **Allow users to log in using the following Single Sign-On (SSO) Service** (Разрешить пользователям вход с использованием следующей службы единого входа) установите флажок **SAML 2.0** и выберите параметр **Расширенная конфигурация** рядом с ним.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Нажмите кнопку **Добавить**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. В разделе **SAML 2.0 Identity providers** (Поставщики удостоверений SAML) выполните следующие действия.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

	а. Введите имя поставщика удостоверений в поле **Отображаемое имя**.

	b. В текстовое поле **Entity Identifier** (идентификатор сущности) вставьте значение **идентификатора сущности** из мастера настройки приложения в Azure AD.

	в) В поле **SAML Binding** (Привязка SAML) укажите значение POST.

	г) В текстовом поле **Security Token Service Endpoint** (Конечная точка службы безопасности маркеров) введите значение **URL-адрес службы единого входа** из мастера настройки приложения Azure AD.

	д. В поле **User ID Attribute Name** (Имя атрибута идентификатора пользователя) введите http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name.

	Е. Чтобы отправить сертификат, скачанный с Azure AD, нажмите кнопку **Upload** (Отправить).

	g. Нажмите кнопку **ОК**.

10. Нажмите кнопку **Сохранить**.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
	![Единый вход в Azure AD][10]

12. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
	![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    в) В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Cezanne HR Software

Чтобы разрешить пользователям Azure AD вход в Cezanne HR Software, их необходимо подготовить для Cezanne HR Software. В случае с Cezanne HR Software подготовка выполняется вручную.

####Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1.  Войдите на корпоративный сайт Cezanne HR Software в качестве администратора.

2.  В левой панели навигации щелкните **System Setup** (Настройка системы). Перейдите в раздел **Управление пользователями**. Выберите **Добавить нового пользователя**.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Новый пользователь")

3.  В разделе **Person Details** (Личные данные) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Новый пользователь")

	а. Установите для параметра **Внутренний пользователь** значение "Выкл.".

	b. В текстовом поле **Имя** введите **Britta**.

    c. В текстовом поле **Фамилия** введите **Simon**.

	г) В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.

4.  В разделе **Account Information** (Сведения об учетной записи) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Новый пользователь")

	а. В текстовом поле **Имя пользователя** введите адрес электронной почты учетной записи Britta Simon.

	b. В текстовом поле **Пароль** введите пароль учетной записи Britta Simon.

	в) В раскрывающемся списке **Security Role** (Роль безопасности) выберите **HR Professional** (Специалист отдела кадров).

	4\.3. Нажмите кнопку **OК**.

5. Перейдите на вкладку **Единый вход** и нажмите кнопку **Добавить** в области **SAML 2.0 Identifiers** (Идентификаторы SAML 2.0).

	![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Пользователь")

6. Выберите значение в списке **Поставщик удостоверений** и в текстовом поле **User Identifier** (Идентификатор пользователя) введите адрес электронной почты учетной записи Britta Simon.

	![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Пользователь")
	
7. Нажмите кнопку **Сохранить**.

	![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Пользователь")


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к Cezanne HR Software.
	
![Назначение пользователя][200]

**Чтобы назначить Britta Simon в Cezanne HR Software, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
    
	![Назначение пользователя][201]

2. В списке приложений выберите **Cezanne HR Software**.
    
	![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
    
	![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
	![Назначение пользователя][205]

### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку Cezanne HR Software на панели доступа, вы автоматически войдете в приложение Cezanne HR Software.

## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
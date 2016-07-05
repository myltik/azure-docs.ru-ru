<properties
	pageTitle="Руководство по интеграции Azure Active Directory с SAP Business ByDesign | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в SAP Business ByDesign."
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
	ms.date="06/20/2016"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с SAP Business ByDesign

В этом руководстве описано, как интегрировать SAP Business ByDesign с Azure Active Directory (Azure AD).

Интеграция SAP Business ByDesign с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению SAP Business ByDesign.
- Вы можете включить автоматический вход пользователей в SAP Business ByDesign (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Business ByDesign, вам потребуется:

- подписка Azure AD;
- подписка SAP Business ByDesign с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление SAP Business ByDesign из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление SAP Business ByDesign из коллекции
Чтобы настроить интеграцию SAP Business ByDesign с Azure AD, необходимо добавить SAP Business ByDesign из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Business ByDesign из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.


3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **SAP Business ByDesign**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. В области результатов выберите **SAP Business ByDesign** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в SAP Business ByDesign с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP Business ByDesign соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Business ByDesign.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP Business ByDesign.

Чтобы настроить и проверить единый вход Azure AD в SAP Business ByDesign, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Business ByDesign](#creating-an-sap-business-bydesign-test-user)** требуется для создания пользователя Britta Simon в SAP Business ByDesign, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении SAP Business ByDesign.

Приложение SAP Business ByDesign ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке**"Атрибут"** приложения. На следующем снимке экрана приведен пример.


**Чтобы настроить единый вход Azure AD в SAP Business ByDesign, выполните следующие действия.**


1. На классическом портале Azure на странице интеграции с приложением **SAP Business ByDesign** в меню в верхней части страницы щелкните **Атрибуты**.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png)


2. В списке атрибутов токена SAML выберите имя атрибута и нажмите кнопку **Изменить**.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png)

3. В диалоговом окне "Изменение атрибута пользователя" выполните следующие действия:

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png)

	а. В списке "Значение атрибута" выберите функцию **ExtractMailPrefix()**.

	b. В списке "Почта" выберите атрибут пользователя, который вы хотите использовать в своей реализации. Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите **user.extensionattribute2**.

	c. Нажмите **Завершено**.
	

4. На странице интеграции с приложением **SAP Business ByDesign** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

5. На странице **Как пользователи должны входить в SAP Business ByDesign?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png)

6. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png)

    а. В текстовое поле **URL-адрес входа** введите URL-адрес для входа в приложение SAP Business ByDesign в формате `https://<servername>.sapbydesign.com`.
	
	b. Нажмите кнопку **Далее**.
 
7. На странице **Настройка единого входа в SAP Business ByDesign** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


8. Чтобы настроить единый вход для приложения, выполните следующие действия.

	а. Войдите на портал SAP Business ByDesign с правами администратора.

	b. Перейдите к элементу **Application and User Management Common Task** (Общие задачи управления приложением и пользователем) и откройте вкладку **Identity Provider** (Поставщик удостоверений).

	c. Щелкните **New Identity Provider** (Новый поставщик удостоверений) и выберите XML-файл метаданных, скачанный с классического портала Azure. Импортируя метаданные, система автоматически отправляет требуемые сертификаты подписи и шифрования.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

	г) Чтобы включить **URL-адрес службы обработчика утверждений** в запрос SAML, выберите **Include Assertion Consumer Service URL** (Включить URL-адрес службы обработчика утверждений).

	д. Щелкните **Activate Single Sign-On** (Активировать единый вход).

	Е. Сохраните изменения.

	g. Перейдите на вкладку **My System** (Моя система).

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

	h. Скопируйте значение поля **SSO URL** (URL-адрес единого входа) и вставьте его в поле **Azure AD Sign On URL** (URL-адрес входа Azure AD).

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

	i. Укажите, сможет ли сотрудник вручную переключаться между входом с помощью учетных данных (идентификатора пользователя и пароля) и единым входом, выбрав **Manual Identity Provider Selection** (Выбор поставщика удостоверений вручную).

	j. В разделе **SSO URL** (URL-адрес единого входа) укажите URL-адрес, который будет использоваться вашими сотрудниками для входа в систему. В раскрывающемся списке URL Sent to Employee (URL-адрес для отправки сотруднику) можно выбрать следующие параметры.
	
	**Non-SSO URL** (URL-адрес без единого входа)
 
	Система отправит сотруднику обычный URL-адрес системы. Сотрудник не сможет входить в систему с помощью единого входа. Вместо этого ему необходимо использовать пароль или сертификат.

	**SSO URL** (URL-адрес единого входа)

	Система отправит сотруднику URL-адрес единого входа. Сотрудник сможет входить в систему с помощью единого входа. Запрос на проверку подлинности перенаправляется через поставщик удостоверений.

	**Automatic Selection** (Автоматический выбор)
 
	Если функция единого входа неактивна, система отправит сотруднику обычный URL-адрес системы. Если функция единого входа активна, система проверит, есть ли у сотрудника пароль. При наличии пароля сотруднику будет отправлен URL-адрес единого входа и URL-адрес другого метода входа. Если пароля нет, сотруднику будет отправлен только URL-адрес единого входа.

	k. Сохраните изменения.

9. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	![Единый вход в Azure AD][10]

10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
 
	![Единый вход в Azure AD][11]


### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** сделайте следующее:
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия:
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получение временного пароля** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png)

8. На диалоговой странице **Получение временного пароля** выполните следующие действия:

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя SAP Business ByDesign

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP Business ByDesign. Для добавления пользователей к платформе SAP Business ByDesign обратитесь в группу поддержки SAP Business ByDesign.

> [AZURE.NOTE] Значение идентификатора имени должно совпадать с полем имени пользователя на платформе SAP Business ByDesign.

### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Business ByDesign.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению SAP Business ByDesign, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **SAP Business ByDesign**.

	![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

4. Из списка пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Business ByDesign на панели доступа, вы автоматически войдете в приложение SAP Business ByDesign.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0622_2016-->
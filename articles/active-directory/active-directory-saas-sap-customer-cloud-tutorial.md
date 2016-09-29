<properties
	pageTitle="Руководство по интеграции Azure Active Directory с SAP Cloud for Customer | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud for Customer."
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
	ms.date="09/09/2016"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с SAP Cloud for Customer

В этом руководстве описано, как интегрировать SAP Cloud for Customer с Azure Active Directory (Azure AD).

Интеграция SAP Cloud for Customer с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к приложению SAP Cloud for Customer;
- вы можете включить автоматический вход пользователей в SAP Cloud for Customer (единый вход) под учетной записью Azure AD;
- вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Cloud for Customer, вам потребуется:

- подписка Azure AD;
- подписка SAP Cloud for Customer с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SAP Cloud for Customer из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление SAP Cloud for Customer из коллекции
Чтобы настроить интеграцию SAP Cloud for Customer с Azure AD, необходимо добавить SAP Cloud for Customer из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud for Customer из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **SAP Cloud for Customer**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_01.png)

7. В области результатов выберите **SAP Cloud for Customer** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Active Directory](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_02.png)



##  Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Cloud for Customer с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP Cloud for Customer соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud for Customer.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud for Customer, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Cloud for Customer](#creating-an-sap-business-bydesign-test-user)** требуется для создания пользователя Britta Simon в SAP Cloud for Customer, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении SAP Cloud for Customer.


**Чтобы настроить единый вход Azure AD в SAP Cloud for Customer, выполните следующие действия:**


1. На классическом портале Azure на странице интеграции с приложением **SAP Cloud for Customer** в меню в верхней части страницы щелкните **Атрибуты**.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_80.png)


2. В списке атрибутов токена SAML выберите имя атрибута и нажмите кнопку **Изменить**.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_84.png)

3. В диалоговом окне **Изменение атрибута пользователя** выполните следующие действия:

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_85.png)


	а. В списке **Значение атрибута** выберите функцию **ExtractMailPrefix()**.
	
	b. В списке **Почта** выберите атрибут пользователя, который вы хотите использовать в своей реализации. Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.

	c. Нажмите **Завершено**.
	

4. На классическом портале Azure на странице интеграции с приложением **SAP Cloud for Customer** щелкните **Настроить единый вход**.
	 
	![Настройка единого входа][6]

5. На странице **Как пользователи должны входить в SAP Cloud for Customer?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_03.png)

6. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_04.png)

    а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение SAP Cloud for Customer в формате `https://<server name>.crm.ondemand.com`.
	
	b. Нажмите кнопку **Далее**.
 
7. На странице **Настройка единого входа в SAP Cloud for Customer** выполните следующие действия:

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


8. В разделе "Конфигурация единого входа" выполните следующие действия:

	а. Войдите на портал SAP Cloud for Customer с правами администратора.

	b. Перейдите к элементу **Application and User Management Common Task** (Общие задачи управления приложением и пользователем) и откройте вкладку **Поставщик удостоверений**.

	c. Щелкните **New Identity Provider** (Новый поставщик удостоверений) и выберите XML-файл метаданных, скачанный на классическом портале Azure. Импортируя метаданные, система автоматически отправляет требуемые сертификаты подписи и шифрования.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_54.png)

	г) В запросе SAML для Azure AD требуется **URL-адрес службы обработчика утверждений**, поэтому установите соответствующий флажок.

	д. Щелкните **Activate Single Sign-On** (Активировать единый вход).

	Е. Сохраните изменения.

	g. Перейдите на вкладку **My System** (Моя система).

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_52.png)

	h. Скопируйте значение поля **SSO URL** (URL-адрес единого входа) и вставьте его в поле **Azure AD Sign On URL** (URL-адрес входа Azure AD).

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_53.png)

	i. Укажите, может ли сотрудник вручную выбрать вход с помощью учетных данных (идентификатора пользователя и пароля) или единый вход, щелкнув **Manual Identity Provider Selection** (Выбор поставщика удостоверений вручную).

	j. В разделе **SSO URL** (URL-адрес единого входа) укажите URL-адрес, который будет использоваться вашими сотрудниками для входа в систему. В списке **URL Sent to Employee** (URL-адрес для отправки сотруднику) можно выбрать следующие параметры:
	
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
В этом разделе описано, как создать на классическом портале Azure тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя в SAP Cloud for Customer

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP Cloud for Customer. Чтобы добавить пользователей платформы SAP Cloud for Customer, обратитесь в службу поддержки этого продукта.

> [AZURE.NOTE] Значение идентификатора имени должно совпадать с полем имени пользователя на платформе SAP Cloud for Customer.

### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Cloud for Customer.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в приложении SAP Cloud for Customer, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **SAP Cloud for Customer**.

	![Настройка единого входа](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud for Customer на панели доступа, вы автоматически войдете в приложение SAP Cloud for Customer.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->
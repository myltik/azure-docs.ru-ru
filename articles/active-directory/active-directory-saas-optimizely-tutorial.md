<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Optimizely | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Optimizely."
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
	ms.date="09/11/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Optimizely

В этом учебнике описано, как интегрировать Optimizely с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Optimizely обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Optimizely.
- Вы можете включить автоматический вход пользователей в Optimizely (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Optimizely, вам потребуется:

- подписка Azure AD;
- подписка **Optimizely** с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Optimizely из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Optimizely из коллекции
Чтобы настроить интеграцию Optimizely с Azure AD, необходимо добавить Optimizely из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Optimizely из коллекции, выполните следующие действия**.

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Optimizely**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. В области результатов выберите **Optimizely** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Optimizely с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Optimizely соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Optimizely. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Optimizely.

Чтобы настроить и проверить единый вход Azure AD в Optimizely, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Optimizely](#creating-an-optimizely-test-user)** требуется для создания в Optimizely пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Optimizely.

Приложение Optimizely ожидает, что утверждения SAML должны содержать атрибут email. Значением email должен быть электронный адрес, распознаваемый Optimizely, который может быть использован для аутентификации в Azure AD. Настройте утверждение email для этого приложения. Управлять значениями этих атрибутов можно на вкладке**Atrributes** (Атрибуты) приложения. На следующем снимке экрана приведен пример.


![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png)


**Чтобы настроить единый вход Azure AD в Optimizely, выполните следующие действия**.

1. На странице интеграции с приложением **Optimizely** классического портала Azure в верхнем меню щелкните **Атрибуты**.
     
    ![Настройка единого входа][5]

2. В диалоговом окне "Атрибуты токена SAML" добавьте атрибут email.

	а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
	
	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

	b. В текстовом поле **Имя атрибута** введите email.

	c. Из списка **Значение атрибута** выберите значение атрибута userprincipalname или любое значение, которое содержит электронный адрес, распознаваемый Azure AD и Optimizely.

	г) Нажмите **Завершено**.
3. В меню вверху щелкните **Быстрый запуск**.

	![Настройка единого входа][6]
4. На классическом портале Azure на странице интеграции с приложением **Optimizely** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][7]

5. На странице **Как пользователи должны входить в Optimizely** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 	
	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    а. В текстовом поле **URL-адрес входа** введите `https://app.optimizely.net/contoso`.

	b. В текстовом поле **Идентификатор** введите `urn:auth0:optimizely:contoso`.

	c. Нажмите кнопку **Далее**.


	> [AZURE.NOTE] Значения параметров **URL-адрес входа** и **Идентификатор** — это только заполнители для фактических значений. Инструкции по получению фактических значений из Optimizely приведены далее в этом учебнике.

7. На странице **Настройка единого входа в Optimizely** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Скопируйте **URL-адрес службы единого входа**.

8. Для настройки единого входа для своего приложения обратитесь к менеджеру по работе с клиентами Optimizely и предоставьте следующие сведения:

    - скачанный сертификат;
    - URL-адрес службы единого входа.
 
    В ответ на ваше электронное сообщение Optimizely предоставит URL-адрес входа (для единого входа, инициируемого поставщиком услуг) и идентификатор (идентификатор сущности поставщика службы).

9. Вернитесь на диалоговую страницу **Настройка параметров приложения** и выполните действия, указанные ниже.

	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

	а. В текстовом поле **URL-адрес входа** введите **URL-адрес единого входа, инициируемого поставщиком услуг**, предоставленный Optimizely.

	b. В текстовом поле **Идентификатор** введите **идентификатор сущности поставщика службы**, предоставленный Optimizely.

    c. Нажмите кнопку **Далее**.

10. На странице **Настройка единого входа в Optimizely** выполните следующие действия.
	
	![Единый вход в Azure AD][10]

    а. Выберите подтверждение конфигурации единого входа.

    b. Нажмите кнопку **Далее**.

11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  	
	![Единый вход в Azure AD][11]

12. В отдельном окне браузера войдите в приложение Optimizely.
13. Щелкните имя своей учетной записи в верхнем правом углу, затем щелкните **Account Settings** (Параметры учетной записи).

	![Единый вход в Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. На вкладке "Account" (Учетная запись) установите флажок **Enable SSO** (Включить единый вход) в области "Single Sign On" (Единый вход) раздела **Overview** (Общие сведения).

	![Единый вход в Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Optimizely

В этом разделе описано, как создать пользователя Britta Simon в приложении Optimizely.

1. На домашней странице выберите вкладку **Collaborators** (Сотрудники).
2. Щелкните **New Collaborator** (Создать сотрудника), чтобы добавить нового сотрудника в проект.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Введите электронный адрес и назначьте роль. Нажмите кнопку **Пригласить**.


	![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Он получит приглашение по электронной почте. Электронный адрес будет использоваться для входа в Optimizely.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Optimizely.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Optimizely, выполните следующие действия**.

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. Из списка приложений выберите **Optimizely**.

	![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке "Все пользователи" выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Optimizely" на панели доступа, вы автоматически войдете в приложение Optimizely.

## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->
<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с The Funding Portal | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в The Funding Portal."
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
	ms.date="09/02/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с The Funding Portal

В этом руководстве описано, как интегрировать The Funding Portal с Azure Active Directory (Azure AD).

Интеграция Azure AD с The Funding Portal обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к The Funding Portal.
- Вы можете включить автоматический вход пользователей в The Funding Portal (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с The Funding Portal, вам потребуется:

- подписка Azure AD;
- подписка на **The Funding Portal** с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление The Funding Portal из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление The Funding Portal из коллекции
Чтобы настроить интеграцию The Funding Portal с Azure AD, необходимо добавить The Funding Portal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить The Funding Portal из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **The Funding Portal**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png)

7. В области результатов выберите **The Funding Portal** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png)

##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в The Funding Portal с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в The Funding Portal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в The Funding Portal. Для этого следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в The Funding Portal.

Чтобы настроить и проверить единый вход Azure AD в The Funding Portal, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя The Funding Portal](#creating-a-the-funding-portal-test-user)** требуется для создания пользователя Britta Simon в The Funding Portal, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении The Funding Portal.

Приложение The Funding Portal ожидает, что утверждения SAML должны содержать атрибут externalId1. Значение externalId1 должно быть распознаваемым значением studentID. Настройте утверждение externalId1 для этого приложения. Управлять значениями этих атрибутов можно на вкладке**Atrributes** (Атрибуты) приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png)

**Чтобы настроить единый вход Azure AD в The Funding Portal, сделайте следующее:**

1. На странице интеграции с приложением **The Funding Portal** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
     
    ![Настройка единого входа][5]

2. В диалоговом окне "Атрибуты токена SAML" добавьте атрибут externalId1.

	а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
	
	![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png)

	b. В текстовом поле **Имя атрибута** введите externalId1.

	c. В списке **Значение атрибута** выберите атрибут для реализации. Например, если значение studentID сохранено в ExtensionAttribute1, выберите атрибут user.extensionattribute1.

	г) Нажмите **Завершено**. Затем щелкните **Применить изменения**.

1. В верхнем меню щелкните **Быстрый запуск**.

	![Настройка единого входа][6]

2. На классическом портале Azure на странице интеграции с приложением **The Funding Portal** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][7]

3. На странице **Как пользователи должны входить в The Funding Portal?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 	
	![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png)

4. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    а. В текстовое поле "URL-адрес для входа" введите URL-адрес в следующем формате: `https://<subdomain>.regenteducation.net/`.

	b. Нажмите кнопку **Далее**.

5. На странице **Настройка единого входа в The Funding Portal** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на свой компьютер.

	![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png)

6. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки The Funding Portal. Они помогут выбрать правильный канал для настройки единого входа. Обратите внимание, что необходимо будет отправить по электронной почте сообщение, прикрепив к нему скачанный файл метаданных info@regenteducation.com.

7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	![Единый вход в Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  	
	![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя The Funding Portal

В этом разделе описано, как создать пользователя Britta Simon в приложении The Funding Portal. Если вы не знаете, как добавить пользователя Britta Simon в The Funding Portal, обратитесь в службу поддержки The Funding Portal с просьбой добавить тестового пользователя и включить единый вход. Отправьте запрос по адресу info@regenteducation.com.

### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к The Funding Portal.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в The Funding Portal, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	![Назначение пользователя][201]

2. В списке приложений выберите **The Funding Portal**.

	![Настройка единого входа](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке "Все пользователи" выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку The Funding Portal на панели доступа, вы автоматически войдете в приложение The Funding Portal.

## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Allocadia | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Allocadia."
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


# Учебник. Интеграция Azure Active Directory с Allocadia

В этом учебнике описано, как интегрировать приложение Allocadia с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Allocadia обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Allocadia.
- Вы можете включить автоматический вход пользователей в Allocadia (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Allocadia, вам потребуется:

- подписка Azure AD;
- подписка Allocadia с активированной функцией единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Allocadia из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Allocadia из коллекции
Чтобы настроить интеграцию Allocadia с Azure AD, необходимо добавить Allocadia из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Allocadia из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Allocadia**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_01.png)

7. В области результатов выберите **Allocadia** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_06.png)

##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Allocadia с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Allocadia соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Allocadia. Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Allocadia.

Чтобы настроить и проверить единый вход Azure AD в Allocadia, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Allocadia](#creating-an-allocadia-test-user)** требуется для создания в Allocadia пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Allocadia.


Приложение Allocadia ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке**"Атрибут"** приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_07.png)

**Чтобы настроить единый вход Azure AD в Hightail, выполните следующие действия:**


1. На странице интеграции с приложением **Allocadia** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_general_80.png)


2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:

	| Имя атрибута | Значение атрибута |
	| --- | --- |    
	| firstname | user.givenname |
    | lastname | user.surname |
	| email | user.mail |
	

	а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_general_81.png)


	b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.

    г) Нажмите **Завершено**.
	

3. В меню вверху щелкните **Быстрый запуск**.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_general_83.png)


4. На странице **Как пользователи должны входить в Allocadia?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 	
	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_03.png)

5. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_04.png)

	а. В поле "Идентификатор" введите URL-адрес, используя следующий шаблон. Для тестовых сред используйте формат URL-адреса **https://na2standby.allocadia.com**, а для рабочих сред — **https://na2.allocadia.com**.

	b. В поле "URL-адрес ответа" введите URL-адрес, используя следующий шаблон. Для тестовых сред используйте формат URL-адреса **https://na2standby.allocadia.com/allocadia/saml/SSO**, а для рабочих сред — **https://na2.allocadia.com/allocadia/saml/SSO**.


6. На странице **Настройка единого входа в Allocadia** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


7.  Чтобы получить помощь при настройке единого входа для своего приложения, обратитесь в [службу поддержки Allocadia](mailTo:support@allocadia.com). Обратите внимание, что для настройки единого входа службой поддержки Allocadia необходимо отправить по электронной почте сообщение, прикрепив к нему скачанный файл метаданных.
 
	> [AZURE.NOTE] Убедитесь, что служба поддержки Allocadia установила значение идентификатора в правильном формате: для тестовых сред — **https://na2standby.allocadia.com**, а для рабочих сред — **https://na2.allocadia.com**.


8. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	![Единый вход в Azure AD][10]

9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  	
	![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD

В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
	
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Allocadia

В этом разделе описано, как создать пользователя Britta Simon в приложении Allocadia. Приложение Allocadia поддерживает JIT-подготовку пользователей. Если вы настроили утверждения, как показано выше в разделе **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)**, то в приложении будет выполняться подготовка пользователей.


> [AZURE.NOTE] Чтобы создать пользователя или несколько пользователей вручную, обратитесь в службу поддержки Allocadia.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Allocadia.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Allocadia, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. Из списка приложений выберите **Allocadia**.

	![Настройка единого входа](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув элемент Allocadia на панели доступа, вы автоматически войдете в приложение Allocadia.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
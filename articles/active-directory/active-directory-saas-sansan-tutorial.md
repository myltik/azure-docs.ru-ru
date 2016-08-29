.<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с SanSan | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и SanSan."
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
	ms.date="07/20/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с SanSan

В этом руководстве описано, как интегрировать SanSan с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SanSan обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к SanSan.
- Вы можете включить автоматический вход пользователей в SanSan (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с SanSan, вам потребуется:

- подписка Azure AD;
- подписка на приложение SanSan с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Microsoft Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SanSan из коллекции.
2. Настройка и проверка единого входа в Microsoft Azure AD


## Добавление SanSan из коллекции.
Чтобы настроить интеграцию SanSan с Azure AD, необходимо добавить SanSan из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SanSan из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **SanSan**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)

7. В области результатов выберите **SanSan** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

##  Настройка и проверка единого входа в Microsoft Azure AD
В этом разделе описывается настройка и проверка единого входа Microsoft Azure AD в SanSan с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SanSan соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SanSan. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SanSan.

Чтобы настроить и проверить единый вход Microsoft Azure AD в SanSan, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Microsoft Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя SanSan](#creating-an-sansan-test-user)** требуется для создания в SanSan пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Microsoft Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа Microsoft Azure AD

В этом разделе описано, как включить единый вход Microsoft Azure AD на классическом портале и настроить его в приложении SanSan.


**Чтобы настроить единый вход Microsoft Azure AD в SanSan, сделайте следующее:**


1. На классическом портале Azure на странице интеграции с приложением **SanSan** щелкните "Настроить единый вход", чтобы открыть диалоговое окно "Настройка единого входа".

	![Настройка единого входа](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png)

2. На странице **How would you like users to sign on to SanSan** (Как пользователи должны входить в SanSan) выберите параметр **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
 	
	![Настройка единого входа](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png)

	а. В текстовом поле **URL-адрес входа** введите URL-адрес в следующем формате:

    | Среда | URL-адрес |
    | :--                     | :-- |
    | Компьютерная сеть | `https://ap.sansan.com/v/saml2/<company name>/acs`|
    | Собственное мобильное приложение | `https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Параметры браузера для мобильных устройств | `https://ap.sansan.com/s/saml2/<company name>/acs` |


	b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Среда | URL-адрес |
    | :--                     | :-- |
    | Компьютерная сеть | `https://ap.sansan.com/v/saml2/<company name>`|
    | Собственное мобильное приложение | `https://internal.api.sansan.com/saml2/<company name>` |
    | Параметры браузера для мобильных устройств | `https://ap.sansan.com/s/saml2/<company name>` |


    в) Нажмите кнопку **Далее**.

4. На странице **Configure single sign-on at SanSan** (Настройка единого входа в SanSan) выполните следующие действия:

	![Настройка единого входа](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5.  Чтобы получить помощь при настройке единого входа для своего приложения, обратитесь в службу поддержки SanSan. Необходимо предоставить им следующие сведения:

	• скачанный **сертификат**;

	• **идентификатор поставщика удостоверений**;

	• **URL-адрес единого входа SAML**;

	• **URL-адрес службы единого выхода**.

> [AZURE.NOTE] Настройка браузера ПК подходит как для мобильного приложения и браузера для мобильных устройств, так и для компьютерной сети.


6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	.![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  	
	.![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD

В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
	
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
	
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя SanSan

В этом разделе описано, как создать пользователя Britta Simon в приложении SanSan. Перед выполнением единого входа все пользователи должны быть подготовлены в приложении SanSan.


> [AZURE.NOTE] Чтобы создать пользователя или несколько пользователей вручную, обратитесь в службу поддержки SanSan.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SanSan.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в SanSan, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	![Назначение пользователя][201]

2. В списке приложений выберите **SanSan**.

	![Настройка единого входа](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Microsoft Azure AD с помощью панели доступа. Щелкнув элемент SanSan на панели доступа, вы автоматически войдете в приложение SanSan.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



.<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->
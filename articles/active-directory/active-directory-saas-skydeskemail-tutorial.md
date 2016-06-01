<properties
	pageTitle="Учебник. Интеграция Azure Active Directory со Skydesk Email | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Skydesk Email."
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
	ms.date="04/22/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory со Skydesk Email

Цель этого учебника — показать, как интегрировать Skydesk Email с Azure Active Directory (Azure AD).

Интеграция Skydesk Email с Azure AD дает приведенные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к Skydesk Email.
- Вы можете включить автоматический вход пользователей в Skydesk Email (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD со Skydesk Email, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Skydesk Email.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Skydesk Email из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Skydesk Email из коллекции
Чтобы настроить интеграцию Skydesk Email с Azure AD, необходимо добавить приложение Skydesk Email из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Skydesk Email из коллекции, выполните указанные ниже действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Skydesk Email**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)

7. В области результатов выберите **Skydesk Email** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Skydesk Email с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Skydesk Email соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Skydesk Email.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Skydesk Email.

Чтобы настроить и проверить единый вход Azure AD в Skydesk Email, вам потребуется выполнить действия в приведенных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Skydesk Email](#creating-a-Skydesk-Email-test-user)** требуется для создания пользователя Britta Simon в Skydesk Email, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Skydesk Email.



**Чтобы настроить единый вход Azure AD в Skydesk Email, выполните указанные ниже действия.**

1. На классическом портале Azure на странице интеграции с приложением **Skydesk Email** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Skydesk Email?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png)


3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
 
	![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png)


    а. В текстовом поле "URL-адрес входа" введите URL-адрес, применяемый пользователями для входа в приложение Skydesk Email, по следующему шаблону: **"https://mail.skydesk.jp/portal/<название\_организации>"**.

    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в Skydesk Email** выполните указанные ниже действия.

	![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы включить единый вход в **Skydesk Email**, выполните указанные ниже действия.
 
    а. Войдите в учетную запись Skydesk Email от имени администратора.

    b. В меню в верхней части страницы нажмите Setup (Настройка) и выберите пункт Org (Организация).

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)

    c. На панели слева выберите пункт Domains (Домены).

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    г) Щелкните Add Domain (Добавить домен).

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    д. Введите доменное имя, а затем проверьте домен.

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    Е. На панели слева выберите пункт **SAML Authentication** (Аутентификация SAML).

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

6. На странице **SAML Authentication** выполните указанные ниже действия.

    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)

	> [AZURE.NOTE] Для использования аутентификации на основе SAML у вас должен быть настроен **проверенный домен** или **URL-адрес портала**. Для URL-адреса портала можно настроить уникальное имя.

	![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)


    а. На классическом портале Azure AD скопируйте значение поля **URL-адрес единого входа SAML**, а затем вставьте его в текстовое поле **URL-адрес входа**.

    b. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого входа**, а затем вставьте его в текстовое поле **URL-адрес выхода**.

    c. Поле **Изменить URL-адрес пароля** является необязательным, поэтому оставьте его пустым.

    г) Щелкните **Получить ключ из файла**, чтобы выбрать скачанный сертификат Skydesk Email, а затем нажмите кнопку **Открыть**, чтобы отправить сертификат.

    д. В поле **Алгоритм** задайте значение **RSA**.

    Е. Нажмите кнопку **ОК**, чтобы сохранить изменения.


7. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  
	![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png)

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия:

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия:

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получение временного пароля** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png)

8. На диалоговой странице **Получение временного пароля** выполните следующие действия:
 
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Skydesk Email

В этом разделе описано, как создать пользователя Britta Simon в приложении Skydesk Email.

а. На левой панели в Skydesk Email выберите пункт **User Access** (Доступ пользователя), а затем введите свое имя пользователя.

![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)


[AZURE.NOTE] Если вам нужно выполнить массовое создание пользователей, обратитесь в службу поддержки Skydesk Email.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Skydesk Email.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению Skydesk Email, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
 
	![Назначение пользователя][201]

2. Из списка приложений выберите **Skydesk Email**.

	![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Skydesk Email на панели доступа, вы автоматически войдете в приложение Skydesk Email.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->
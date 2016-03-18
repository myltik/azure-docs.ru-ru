<properties
	pageTitle="Руководство по интеграции Azure Active Directory с Novatus | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в LearnUpon."
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
	ms.date="02/11/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с LearnUpon

Цель этого руководства — показать, как интегрировать LearnUpon с Azure Active Directory (Azure AD).<br>Интеграция LearnUpon с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LearnUpon.
- Вы можете включить автоматический вход пользователей в LearnUpon (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory. 
- 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с LearnUpon, вам потребуется:

- подписка Azure AD;
- подписка LearnUpon с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление LearnUpon из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление LearnUpon из коллекции
Чтобы настроить интеграцию LearnUpon с Azure AD, необходимо добавить LearnUpon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LearnUpon из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **LearnUpon**.<br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png) <br>
7. В области результатов выберите **LearnUpon** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)<br>
##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в LearnUpon с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в LearnUpon соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LearnUpon.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LearnUpon.

Чтобы настроить и проверить единый вход Azure AD в LearnUpon, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя LearnUpon](#creating-a-learnupon-test-user)** требуется для создания в LearnUpon пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение LearnUpon.



**Чтобы настроить единый вход Azure AD в LearnUpon, выполните следующие действия.**

1. На странице интеграции с приложением **LearnUpon** портала Azure AD щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в LearnUpon?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png)<br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) <br>


    а. В текстовом поле "URL-адрес ответа" введите URL-адрес службы Assertion Consumer Service в следующем формате: **https://<название\_компании>.learnupon.com/saml/consumer**


4. На странице **Настройка единого входа в LearnUpon** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на стороне LearnUpon.

    b. Нажмите кнопку **Далее**.


5. Откройте другое окно браузера и войдите в LearnUpon с правами администратора, чтобы настроить **единый вход SAML** на стороне LearnUpon. После входа в LearnUpon вы увидите экран следующего вида. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) <br>

	а. Щелкните вкладку **Параметры**, чтобы открыть окно параметров.<br> б. Щелкните **Единый вход SAML**<br>. в. Щелкните **Общие параметры**, чтобы настроить параметры SAML. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) <br> г. Заполните форму **Общие параметры**, как показано ниже: <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) <br> г1. Установите флажок **Включено**, чтобы включить SAML на этом портале<br> г2. Выберите **версию 2.0**<br> г3. Выберите параметр **не пропускать условия**<br> г4. **Имя параметра POST маркера SAML** — это имя параметра POST запроса к указанному выше URL-адресу клиента SAML, содержащему требующее проверки и аутентификации утверждение SAML, например **SAMLResponse** <br> г5. **Формат идентификатора имени** указывает, в какой части утверждения SAML находится идентификатор пользователя (адрес электронной почты), например **urn:oasis:names:tc:SAML:1.1:nameid- format:emailAddress**.<br> г6. **Определять расположение поставщика**: страница, на которую будут отправляться пользователи, щелкнувшие отправленный вами значок на экране входа на портал.<br> г7. Скопируйте **URL-адрес службы единого выхода** на экране конфигурации Azure и вставьте его в поле **URL-адрес выхода**. <br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_09.png) <br> г8. Щелкните ссылку **Управление отпечатками** в разделе "Отпечаток сертификата", чтобы отправить отпечаток сертификата. <br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_10.png) <br> г9. Нажмите кнопку "Сохранить". д. Щелкните **Параметры пользователя**, чтобы настроить параметры пользователя SAML. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) <br> д1. **Формат идентификатора имени** указывает, в какой части утверждения SAML находится имя пользователя, например **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**. д2. **Формат идентификатора фамилии** указывает, в какой части утверждения SAML находится фамилия пользователя, например **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ surname**.


6. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя LearnUpon

Цель этого раздела — создать пользователя с именем Britta Simon в LearnUpon. Приложение LearnUpon поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению LearnUpon (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки LearnUpon.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к LearnUpon. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в LearnUpon, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **LearnUpon**. <br><br>![Настройка единого входа](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент LearnUpon на панели доступа, вы автоматически войдете в приложение LearnUpon.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0218_2016-->
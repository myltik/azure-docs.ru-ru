<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с QuickHelp | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и QuickHelp."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="markusvi"/>


# Руководство. Интеграция Azure Active Directory с QuickHelp

Цель этого руководства — показать, как интегрировать QuickHelp с Azure Active Directory (Azure AD). <br>Интеграция QuickHelp с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к QuickHelp; 
- вы можете включить автоматический вход пользователей в QuickHelp (единый вход) под учетной записью Azure AD;
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с QuickHelp, вам потребуется:

- подписка Azure AD;
- подписка на QuickHelp с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление QuickHelp из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление QuickHelp из коллекции
Чтобы настроить интеграцию QuickHelp с Azure AD, необходимо добавить QuickHelp из коллекции в список управляемых приложений SaaS.

**Чтобы добавить QuickHelp из коллекции, выполните следующие действия.**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога щелкните **Приложения** в верхнем меню.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **QuickHelp**.<br><br> ![Приложения][5]<br>
7. В области результатов выберите **QuickHelp** и нажмите кнопку **Завершить**, чтобы добавить приложение.<br><br>![Приложения][500]<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в QuickHelp с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в QuickHelp соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в QuickHelp.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в QuickHelp.
 
Чтобы настроить и проверить единый вход Azure AD в QuickHelp, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя QuickHelp](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в QuickHelp, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение QuickHelp.<br>

**Чтобы настроить единый вход Azure AD в QuickHelp, выполните следующие действия:**

1. На странице интеграции с приложением **QuickHelp** портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в QuickHelp?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Единый вход в Azure AD][7] <br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка параметров приложения][8] <br>
 
     а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт QuickHelp (например, * https://quickhelp.com/bsiazure/#/home/assignedContent*)).

     >[AZURE.NOTE]Обратитесь в службу поддержки QuickHelp, если вы не знаете URL-адрес входа.

     b. Нажмите кнопку **Далее**.

4. Скачайте файл метаданных **QuickHelp** и сохраните его на компьютере: [https://quickhelp.blob.core.windows.net/metadata/QuickhelpSamlMetadataBS.xml](https://quickhelp.blob.core.windows.net/metadata/QuickhelpSamlMetadataBS.xml).
 
4. На странице **Настройка единого входа в QuickHelp** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере. <br><br>![Что такое Azure AD Connect?][9] <br>



1. Выполните вход на сайт компании QuickHelp в качестве администратора.

2. В верхнем меню щелкните **Администратор**.<br><br>![Настройка единого входа][21]<br>


1. В меню **Администратор QuickHelp** щелкните **Параметры**. <br><br>![Настройка единого входа][22]<br>

1. Щелкните **Параметры проверки подлинности**.

1. На странице **Параметры проверки подлинности** выполните следующие действия. <br><br>![Настройка единого входа][23]<br>

    а. Для параметра **Тип SSO** выберите значение **WSFederation**.

    b. Чтобы передать скачанный файл метаданных Azure, нажмите кнопку **Обзор**, перейдите к файлу и щелкните **Отправка метаданных**.

    г) В текстовое поле **Адрес электронной почты** введите ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

6. На портале Azure AD выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Что такое Azure AD Connect?][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Что такое Azure AD Connect?][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать тестового пользователя с именем Britta Simon на портале Azure.<br> В списке пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в левой области навигации щелкните **Active Directory**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png)<br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.<br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png)<br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя QuickHelp

Цель этого раздела — создать пользователя с именем Britta Simon в QuickHelp.

В этом руководстве новые пользователи импортируются из CSV-файла со следующей структурой:

|FirstName|LastName|Email|Department|Название|
|---|---|---|---|---|
|Britta|Simon|BritaSimon@Fabrikam.com|||

<br><br>![Создание тестового пользователя QuickHelp][26]<br>

Необходимо создать CSV-файл с этой структурой, где в качестве значений указаны значения для **Britta Simon** в тестовой среде Azure Active Directory.



**Чтобы создать пользователя с именем Britta Simon в QuickHelp, выполните следующие действия.**

1. Создайте CSV-файл, следуя приведенным выше инструкциям. 
 
2. Выполните вход на сайт компании QuickHelp в качестве администратора. <br><br>![Создание тестового пользователя QuickHelp][21]<br>


3. В меню **Администратор QuickHelp** щелкните **Пользователи** и затем **Создать**. <br><br>![Создание тестового пользователя QuickHelp][24]<br>


4. В поле **Содержимое** выберите значение **Пользователь**, а затем нажмите кнопку **Импорт**. <br><br>![Создание тестового пользователя QuickHelp][25]<br>

5. Чтобы импортировать CSV-файл, нажмите кнопку **Обзор**, перейдите к файлу и нажмите кнопку **Далее**. <br><br>![Создание тестового пользователя QuickHelp][26]<br>

6. Просмотрите состояние на странице сводки и нажмите кнопку **Готово**. <br><br>![Создание тестового пользователя QuickHelp][27]<br>


Если пользователь Britta был успешно импортирован, вы увидите его в списке пользователей. <br><br>![Создание тестового пользователя QuickHelp][28]<br>



### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к QuickHelp. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в QuickHelp, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **QuickHelp**. <br><br>![Назначение пользователя][202] <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув плитку QuickHelp на панели доступа, вы автоматически войдете в приложение QuickHelp.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png

<!---HONumber=Oct15_HO4-->
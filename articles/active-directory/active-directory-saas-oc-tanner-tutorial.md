<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с O.C. Tanner — AppreciateHub | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и O.C. Tanner — AppreciateHub."
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
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с O.C. Tanner — AppreciateHub

Цель данного руководства — показать, как интегрировать O.C. Tanner — AppreciateHub с Azure Active Directory (Azure AD).<br>Интеграция O.C. Tanner — AppreciateHub с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к O.C. Tanner — AppreciateHub 
- Вы можете включить автоматический вход пользователей в O.C. Tanner — AppreciateHub (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с O.C. Tanner — AppreciateHub, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа O.C. Tanner — AppreciateHub.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление O.C. Tanner — AppreciateHub из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление O.C. Tanner — AppreciateHub из коллекции
Чтобы настроить интеграцию O.C. Tanner — AppreciateHub с Azure AD, необходимо добавить O.C. Tanner — AppreciateHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить O.C. Tanner — AppreciateHub из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1] <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню. <br><br> ![Приложения][2] <br>

4. В нижней части страницы нажмите кнопку **Добавить**. <br><br> ![Приложения][3] <br>

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**. <br><br> ![Приложения][4] <br>

6. В поле поиска введите **O.C. Tanner — AppreciateHub**. <br><br> ![Приложения][5] <br>

7. В области результатов выберите **O.C. Tanner — AppreciateHub** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Приложения][25] <br>




##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в O.C. Tanner — AppreciateHub соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в O.C. Tanner — AppreciateHub.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в O.C. Tanner — AppreciateHub.
 
Чтобы настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя O.C. Tanner — AppreciateHub](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в O.C. Tanner — AppreciateHub, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение O.C. Tanner — AppreciateHub.<br>

**Чтобы настроить единый вход Azure AD в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. На классическом портале Azure на странице **O.C. Tanner — AppreciateHub** портала Azure AD нажмите **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.<br><br> ![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в O.C. Tanner — AppreciateHub** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.<br><br> ![Единый вход в Azure AD][7]

3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка параметров приложения][8]
 
     а. Откройте файл метаданных, используя следующую ссылку: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Найдите узел **md:AssertionConsumerService**.

     c. Скопируйте значение атрибута **Location**. <br><br>![Настройка параметров приложения][12]
     
     d. Вставьте значение, полученное на предыдущем шаге, в текстовое поле **URL-адрес входа**.

     > [AZURE.NOTE] Если у вас возникли проблемы с получением URL-адреса ответа из файла метаданных, обратитесь в службу поддержки O.C. Tanner — AppreciateHub по адресу [sso@octanner.com](mailto:sso@octanner.com).

     д. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в O.C. Tanner — AppreciateHub** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере.<br><br>![Что такое Azure AD Connect?][9]

5. Отправьте файл метаданных службе поддержки O.C. Tanner — AppreciateHub по адресу xyz и попросите активировать для вас единый вход.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Что такое Azure AD Connect?][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Что такое Azure AD Connect?][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в верхнем меню.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя O.C. Tanner — AppreciateHub

Цель этого раздела — создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub.

**Чтобы создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. Попросите службу поддержки OC Tanner создать пользователя, у которого значение атрибута nameID совпадает с именем пользователя Simon Britta в Azure AD.


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к O.C. Tanner — AppreciateHub. <br><br>![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.<br> <br><br>![Назначение пользователя][201]
2. В списке приложений выберите **O.C. Tanner — AppreciateHub**. <br><br>![Назначение пользователя][202]
1. В верхнем меню щелкните **Пользователи**.<br> <br><br>![Назначение пользователя][203]
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув плитку O.C. Tanner — AppreciateHub на панели доступа, вы автоматически войдете в приложение O.C. Tanner — AppreciateHub.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0302_2016-->
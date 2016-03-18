<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Workrite | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Workrite."
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
	ms.date="02/22/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Workrite

Цель этого руководства — показать, как интегрировать Workrite с Azure Active Directory (Azure AD).<br>Интеграция Workrite с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Workrite; 
- вы можете включить автоматический вход пользователей в Workrite (единый вход) под учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Workrite, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Workrite.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление Workrite из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление Workrite из коллекции
Чтобы настроить интеграцию Workrite с Azure AD, необходимо добавить Workrite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workrite из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Workrite**.<br><br> ![Приложения][5]<br>
7. В области результатов выберите **Workrite** и нажмите кнопку **Завершить**, чтобы добавить приложение.<br><br>![Приложения][500]<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Workrite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Workrite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workrite.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workrite.
 
Чтобы настроить и проверить единый вход Azure AD в Workrite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Workrite](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в Workrite, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Workrite.<br>

**Чтобы настроить единый вход Azure AD в Workrite, выполните следующие действия.**

1. На странице интеграции с приложением **Workrite** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Workrite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Единый вход в Azure AD][7] <br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Единый вход в Azure AD][8] <br>
 
     а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт Workrite (например, **https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*)).

     > [AZURE.NOTE] Обратитесь в службу поддержки Workrite ([support@workrite.co.uk](mailto:support@workrite.co.uk)), если вы не знаете URL-адрес входа.

     b. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Workrite** выполните следующие действия. <br><br>![Единый вход в Azure AD][9] <br>

    а. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.

    b. Обратитесь в службу поддержки Workrite по адресу [support@workrite.co.uk](mailto:support@workrite.co.uk), предоставьте ее сотруднику скачанный сертификат, **URL-адрес издателя** (идентификатор сущности), **URL-адрес службы единого входа**, **URL-адрес единого выхода**, а затем попросите его настроить единый вход для вашего приложения Workrite.

    c. Нажмите кнопку **Далее**.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя Workrite

Цель этого раздела — создать пользователя с именем Britta Simon в Workrite.

**Чтобы создать пользователя с именем Britta Simon в Workrite, выполните следующие действия.**

1. Выполните вход на сайт компании Workrite в качестве администратора.

2. В области навигации щелкните **Администратор**. <br><br>![Назначение пользователя][400]<br>

3. Перейдите в раздел быстрых ссылок и нажмите кнопку **Создать пользователя**. <br><br>![Назначение пользователя][401]<br>

4. В диалоговом окне **Создание пользователя** выполните следующие действия. <br><br>![Назначение пользователя][402]<br>

    а. Заполните поля **Адрес электронной почты**, **Имя** и **Фамилия** данными действительного пользователя Azure AD, которого необходимо подготовить.

    b. Выберите значение **Администратор клиента** в поле **Выберите роль**.

    c. Щелкните **Сохранить**.


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к Workrite. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Workrite, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>
2. В списке приложений выберите **Workrite**. <br><br>![Назначение пользователя][202] <br>
1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>
1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув плитку Workrite на панели доступа, вы автоматически войдете в приложение Workrite.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png

<!---HONumber=AcomDC_0224_2016-->
<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Insperity ExpensAble | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Insperity ExpensAble."
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
	ms.date="03/10/2016"
	ms.author="jeedes"/>

# Учебник. Интеграция Azure Active Directory с Insperity ExpensAble

Цель этого учебника — показать, как интегрировать Insperity ExpensAble с Azure Active Directory (Azure AD).<br>Интеграция Insperity ExpensAble с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Insperity ExpensAble.
- Вы можете включить автоматический вход пользователей в Insperity ExpensAble (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Insperity ExpensAble, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Insperity ExpensAble.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Insperity ExpensAble из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Insperity ExpensAble из коллекции
Чтобы настроить интеграцию Insperity ExpensAble с Azure AD, необходимо добавить Insperity ExpensAble из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Insperity ExpensAble из коллекции, выполните указанные далее действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Insperity ExpensAble**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_01.png)<br>
7. В области результатов выберите **Insperity ExpensAble** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_02.png)<br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Insperity ExpensAble с использованием тестового пользователя Britta Simon.

Для включения единого входа в Azure AD необходимо знать, какой пользователь в приложении Insperity ExpensAble соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Insperity ExpensAble.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Insperity ExpensAble.

Чтобы настроить и проверить единый вход Azure AD в Insperity ExpensAble, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Insperity ExpensAble](#creating-a-insperityexpensable-test-user)** требуется для создания пользователя Britta Simon в Insperity ExpensAble, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Insperity ExpensAble.



**Чтобы настроить единый вход Azure AD в Insperity ExpensAble, выполните следующие действия.**

1. На странице интеграции с приложением **Insperity ExpensAble** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Insperity ExpensAble** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.<br><br> ![Настройка единого входа](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_03.png) <br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_04.png) <br>


    а. В текстовое поле "URL-адрес входа" введите URL-адрес, используемый для входа в приложение Insperity ExpensAble, в формате **"https://server.expensable.com/esapp/Authenticate?companyId=</COMPANYID/>"**.


4. На странице **Настройка единого входа в Insperity ExpensAble** выполните следующие действия: <br><br>![Настройка единого входа](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы получить данные единого входа, настроенные для вашего приложения, обратитесь в службу поддержки Insperity ExpensAble. После назначения обращения отправьте по электронной почте скачанный файл сертификата. Также укажите URL-адрес издателя, URL-адрес службы выхода, чтобы настроить их для интеграции единого входа.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Insperity ExpensAble

Цель этого раздела — создать пользователя с именем Britta Simon в Insperity ExpensAble. Обратитесь в службу поддержки Insperity ExpensAble, чтобы добавить пользователей в учетную запись Insperity ExpensAble.


> [AZURE.NOTE] Если вам требуется вручную создать пользователя, необходимо обратиться в службу поддержки Insperity ExpensAble.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления ей доступа к Insperity ExpensAble. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Insperity ExpensAble, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **Insperity ExpensAble**. <br><br>![Настройка единого входа](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_50.png) <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Insperity ExpensAble на панели доступа, вы автоматически войдете в приложение Insperity ExpensAble.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0316_2016-->
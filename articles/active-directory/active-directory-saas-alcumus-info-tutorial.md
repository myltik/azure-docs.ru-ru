<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Alcumus Info Exchange | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Alcumus Info Exchange."
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
	ms.date="10/06/2015"
	ms.author="markusvi"/>


# Учебник. Интеграция Azure Active Directory с Alcumus Info Exchange

Цель этого учебника — показать, как интегрировать Alcumus Info Exchange с Azure Active Directory (Azure AD).<br>Интеграция Alcumus Info Exchange с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Alcumus Info Exchange. 
- Вы можете включить автоматический вход пользователей в Alcumus Info Exchange (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Alcumus Info Exchange, вам потребуется:

- подписка [Azure AD](http://azure.microsoft.com/);
- подписка на [Alcumus Info Exchange](http://www.alcumusgroup.com/) с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас не развернута среда Azure AD, вы можете получить пробную версию на месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление Alcumus Info Exchange из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление Alcumus Info Exchange из коллекции
Чтобы настроить интеграцию Alcumus Info Exchange с Azure AD, вам нужно добавить Alcumus Info Exchange из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Alcumus Info Exchange из коллекции, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в верхнем меню представления каталога щелкните **Приложения**. <br><br>![Приложения][2]<br>

4. В нижней части страницы нажмите кнопку **Добавить**. <br><br>![Приложения][3]<br>

5. В диалоговом окне **Что необходимо сделать** выберите **Добавить приложение из коллекции**. <br><br>![Приложения][4]<br>

6. В поле поиска введите **Alcumus Info Exchange**. <br><br>![Приложения][5]<br>

7. В области результатов выберите **Alcumus Info Exchange** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Приложения][400]<br>



##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Alcumus Info Exchange с использованием тестового пользователя Britta Simon.

Для использования единого входа в Azure AD необходимо знать, какой пользователь в Alcumus Info Exchange соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Alcumus Info Exchange.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Alcumus Info Exchange.
 
Чтобы настроить и проверить единый вход Azure AD в Alcumus Info Exchange, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)** требуется для создания пользователя Britta Simon в Alcumus Info Exchange, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение Alcumus Info Exchange.<br>

**Чтобы настроить единый вход Azure AD в Alcumus Info Exchange, выполните следующие действия:**

1. На странице интеграции с приложением **Alcumus Info Exchange** портала Azure AD нажмите **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.<br><br> ![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Alcumus Info Exchange** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.<br><br> ![Единый вход в Azure AD][7]

3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия: <br><br>![Единый вход в Azure AD][8]<br>
 
     3\.1. В текстовое поле **URL-адрес ответа** введите URL-адрес получателя, настроенный для вас службой поддержки Alcumus Info Exchange.

     >[AZURE.NOTE]Если вы не знаете, какое значение нужно ввести, обратитесь в службу поддержки Alcumus Info Exchange по адресу [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

     3\.2. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Alcumus Info Exchange** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере.<br><br>![Что такое Azure AD Connect?][9]

5. Отправьте файл метаданных службе поддержки Alcumus Info Exchange по адресу [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com) и попросите активировать для вас единый вход.


6. На портале Azure AD выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Что такое Azure AD Connect?][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Что такое Azure AD Connect?][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать тестового пользователя с именем Britta Simon на портале Azure.<br> В списке пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, нажмите **Пользователи** в меню вверху.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку Далее.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия: <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)
  1. В текстовое поле **Имя** введите **Britta**.  
  2. В текстовое поле **Фамилия** введите **Simon**.
  3. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получение временного пароля** нажмите кнопку **Создать**. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
 
8. На диалоговой странице **Получение временного пароля** выполните следующие действия:<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. Запишите значение в поле **Новый пароль**.
  2. Нажмите **Завершено**.   

  
 
### Создание тестового пользователя Alcumus Info Exchange

Цель этого раздела — создать пользователя с именем Britta Simon в Alcumus Info Exchange.

**Чтобы создать пользователя с именем Britta Simon в Alcumus Info Exchange, выполните следующие действия:**

1. Обратитесь в службу поддержки Alcumus Info Exchange по адресу [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить единый вход в Azure для пользователя Britta Simon, предоставив этому пользователю доступ к Alcumus Info Exchange. <br><br>![Назначение пользователя][200]

**Чтобы назначить Britta Simon в Alcumus Info Exchange, выполните следующие действия:**

1. Чтобы открыть представление приложений, в верхнем меню представления каталога на портале Azure щелкните **Приложения**.<br> <br><br>![Назначение пользователя][201]
2. В списке приложений выберите **Alcumus Info Exchange**. <br><br>![Назначение пользователя][202]
1. В меню вверху нажмите **Пользователи**.<br> <br><br>![Назначение пользователя][203]
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу нажмите **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув элемент Alcumus Info Exchange на панели доступа, вы автоматически войдете в приложение Alcumus Info Exchange.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=Oct15_HO3-->
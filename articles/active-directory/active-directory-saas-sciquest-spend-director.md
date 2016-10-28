<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с SciQuest Spend Director | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и SciQuest Spend Director."
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
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с SciQuest Spend Director

Цель этого учебника — показать, как интегрировать приложение SciQuest Spend Director с Azure Active Directory (Azure AD). Интеграция SciQuest Spend Director с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к SciQuest Spend Director.
- Вы можете включить автоматический вход пользователей в SciQuest Spend Director (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с SciQuest Spend Director, вам потребуется следующее:

- подписка Azure AD;
- подписка SciQuest Spend Director с включенным единым входом.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SciQuest Spend Director из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление SciQuest Spend Director из коллекции
Чтобы настроить интеграцию SciQuest Spend Director с Azure AD, вам потребуется добавить SciQuest Spend Director из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SciQuest Spend Director из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **sciQuest spend director**.

	![Приложения][5]

7. В области результатов выберите **SciQuest Spend Director** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Приложения][6]


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход в Azure AD с SciQuest Spend Director с использованием тестового пользователя с именем "Britta Simon".

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SciQuest Spend Director соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в SciQuest Spend Director. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SciQuest Spend Director.
 
Чтобы настроить и проверить единый вход в Azure AD в SciQuest Spend Director, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя SciQuest Spend Director](#creating-a-halogen-software-test-user)** требуется для создания дублирующего Britta Simon пользователя в SciQuest Spend Director, связанного с ее представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход в Azure AD на классическом портале Azure и настроить единый вход в приложение SciQuest Spend Director.

**Чтобы настроить единый вход в Azure AD с помощью SciQuest Spend Director, выполните следующие действия:**

1. На странице интеграции с приложением **SciQuest Spend Director** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][8]

2. На странице **Как пользователи должны входить в SciQuest Spend Director?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][9]

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка параметров приложения][10]
 
     3\.1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение SciQuest Spend Director, применяя шаблон *https://.*sciquest.com/.**.

     3\.2. В текстовом поле **URL-адрес ответа** введите то же значение, что и в поле **URL-адрес входа**.

     3\.3. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в SciQuest Spend Director** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.

	![Что такое Azure AD Connect?][11]

5. Обратитесь в службу поддержки SciQuest, чтобы включить этот метод проверки подлинности с использованием скачанных выше метаданных.

6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

	![Что такое Azure AD Connect?][15]

10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Что такое Azure AD Connect?][100]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Что такое Azure AD Connect?][101]

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Что такое Azure AD Connect?][102]

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Что такое Azure AD Connect?][103]

	а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
  
	b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
  
	c. Нажмите кнопку Далее.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Что такое Azure AD Connect?][104]

	а. В текстовом поле **Имя** введите **Britta**.
  
	b. В текстовом поле **Фамилия** введите **Simon**.
  
	c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
  
	г) В списке **Роль** выберите **Пользователь**.
  
	д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Что такое Azure AD Connect?][105]

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Что такое Azure AD Connect?][106]

	а. Запишите значение поля **Новый пароль**.
  
	b. Нажмите **Завершено**.
  
 
### Создание тестового пользователя SciQuest Spend Director

Цель этого раздела — создать пользователя с именем Britta Simon в SciQuest Spend Director.

Для этого обратитесь в службу поддержки SciQuest Spend Director и предоставьте им сведения о тестовой учетной записи.

Вы также можете использовать JIT-подготовку — функцию единого входа, поддерживаемую SciQuest Spend Director. Если функция JIT-подготовки включена, SciQuest Spend Director автоматически создает пользователей при попытке единого входа, если они еще не созданы. Эта функция устраняет необходимость вручную создавать дублирующих пользователей для единого входа.

Чтобы включить JIT-подготовку, обратитесь в службу поддержки SciQuest Spend Director.
  

### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ей доступ к SciQuest Spend Director.

![Что такое Azure AD Connect?][200]

**Чтобы назначить Britta Simon в SciQuest Spend Director, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Что такое Azure AD Connect?][201]

2. В списке приложений выберите **SciQuest Spend Director**.

	![Что такое Azure AD Connect?][202]

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Что такое Azure AD Connect?][203]

1. В списке пользователей выберите **Britta Simon**.

	![Что такое Azure AD Connect?][204]

2. На панели инструментов внизу щелкните **Назначить**.

	![Что такое Azure AD Connect?][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Нажав элемент SciQuest Spend Director на панели доступа, вы автоматически войдете в приложение SciQuest Spend Director.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

<!---HONumber=AcomDC_0907_2016-->
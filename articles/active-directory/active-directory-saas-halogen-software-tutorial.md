<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Halogen Software"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Halogen Software."
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
	ms.date="07/19/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Halogen Software

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Halogen Software.

Интеграция Halogen Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Halogen Software.
- Вы можете включить автоматический вход пользователей в Halogen Software (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Halogen Software, вам потребуется следующее:

- подписка Azure AD;
- подписка Halogen Software с включенным единым входом.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Halogen Software из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление Halogen Software из коллекции.
Чтобы настроить интеграцию Halogen Software с Azure AD, вам потребуется добавить Halogen Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Halogen Software из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **halogen software**.

	![Приложения][5]

7. В области результатов выберите **Halogen Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.



##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход в Azure AD с Halogen Software с использованием тестового пользователя с именем "Britta Simon".

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Halogen Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Halogen Software.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Halogen Software.
 
Чтобы настроить и проверить единый вход в Azure AD в Halogen Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Halogen Software](#creating-a-halogen-software-test-user)** требуется для создания дублирующего Britta Simon пользователя в Halogen Software, связанного с ее представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход в Azure AD на классическом портале Azure и настроить единый вход через приложение Halogen Software.


**Чтобы настроить единый вход в Azure AD с помощью Halogen Software, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Halogen Software** нажмите кнопку **Настроить единый вход**. Откроется диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][8]

2. На странице **Как пользователи должны входить в Halogen Software?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][9]

3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.

	![Настройка параметров приложения][10]
 
     a. В текстовое поле **URL-адрес входа** введите URL-адрес, с помощью которого пользователи входят в приложение Halogen Software, в следующем формате: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Halogen Software** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере.
	
	![Что такое Azure AD Connect?][11]

5. В отдельном окне браузера войдите в приложение **Halogen Software** под учетной записью администратора.

6. Откройте вкладку **Параметры**.

	![Что такое Azure AD Connect?][12]


7. На панели навигации слева щелкните **Настройка SAML**.

	![Что такое Azure AD Connect?][13]

8. На странице **Настройка SAML** сделайте следующее: ![Что такое Azure AD Connect?][14]

    а. В качестве значения поля **Уникальный идентификатор** выберите **NameID**.

    b. В качестве значения поля **Уникальный идентификатор сопоставляется с** выберите **Имя пользователя**.

    c. Для отправки скачанного файла метаданных нажмите кнопку **Обзор**, чтобы выбрать файл, а затем нажмите **Отправить файл**.

    г) Чтобы проверить конфигурацию, нажмите кнопку **Запустить тест**.

    > [AZURE.NOTE] Подождите, пока не появится сообщение "*Проверка SAML завершена. Закройте это окно*". Закройте окно браузера. Флажок **Включить SAML** установлен, только если проверка завершена.

    д. Выберите **Включить SAML**.
    
    Е. Нажмите кнопку **Сохранить изменения**.


9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

	![Что такое Azure AD Connect?][15]

10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Что такое Azure AD Connect?][16]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Что такое Azure AD Connect?][100]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы вывести на экран список пользователей, щелкните **Пользователи** в меню вверху.

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

    а. Запишите значение поля **Новый пароль**. b. Нажмите **Завершено**.
  
 
### Создание тестового пользователя Halogen Software

Цель этого раздела — создать пользователя с именем Britta Simon в Halogen Software.

**Чтобы создать пользователя с именем Britta Simon в Halogen Software, выполните следующие действия:**

1. Войдите в приложение **Halogen Software** под учетной записью администратора.

2. Нажмите вкладку **Центр пользователей**, затем нажмите **Создать пользователя**.

	![Что такое Azure AD Connect?][300]

3. На странице диалогового окна **Новый пользователь** выполните следующие действия.

	![Что такое Azure AD Connect?][301]

    а. В текстовом поле **Имя** введите **Britta**.
  
    б) В текстовое поле **Фамилия** введите **Simon**.
  
    c. В текстовое поле **Имя пользователя** введите **имя пользователя Britta Simon на классическом портале Azure**.
  
    г) В текстовом поле **Пароль** введите пароль для пользователя Britta.
  
    д. Щелкните **Сохранить**.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к Halogen Software.

![Что такое Azure AD Connect?][200]

**Чтобы назначить Britta Simon в Halogen Software, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Что такое Azure AD Connect?][201]

2. В списке приложений выберите **Halogen Software**.

	![Что такое Azure AD Connect?][202]

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Что такое Azure AD Connect?][203]

1. Из списка пользователей выберите **Britta Simon**.

	![Что такое Azure AD Connect?][204]

2. На панели инструментов внизу щелкните **Назначить**.

	![Что такое Azure AD Connect?][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Нажав элемент Halogen Software на панели доступа, вы автоматически войдете в приложение Halogen Software.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=AcomDC_0720_2016-->
<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Flatter Files | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Flatter Files."
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с Flatter Files

Цель этого учебника — показать, как интегрировать Flatter Files с Azure Active Directory (Azure AD).<br>Интеграция Flatter Files с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Flatter Files. 
- Вы можете включить автоматический вход пользователей в Flatter Files (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Flatter Files, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа в Flatter Files.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление Flatter Files из коллекции 
2. Настройка и проверка единого входа в Azure AD.


## Добавление Flatter Files из коллекции
Чтобы настроить интеграцию Flatter Files с Azure AD, необходимо добавить Flatter Files из коллекции в список управляемых приложений SaaS.

**Как добавить Flatter Files из коллекции**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Flatter Files**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)<br>
7. В области результатов выберите **Flatter Files** и щелкните **Завершить**, чтобы добавить приложение.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)<br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Flatter Files с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Flatter Files соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Flatter Files.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Flatter Files.
 
Чтобы настроить и проверить единый вход Azure AD в Flatter Files, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)**. Необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Flatter Files](#creating-a-halogen-software-test-user)**. Требуется для создания в Flatter Files пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение Flatter Files. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Для настройки единого входа в приложение Flatter Files вам потребуется зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Flatter Files по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Как настроить единый вход Azure AD в Flatter Files**

1. На классическом портале Azure AD на странице интеграции с приложением **Flatter Files** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.<br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Flatter Files?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) <br>

3. На странице **Настройка параметров приложения** нажмите кнопку **Далее**. <br><br>![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) <br>

    > [AZURE.NOTE] Приложение Flatter Files использует один и тот же URL-адрес единого входа для всех клиентов: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
 
 
4. На странице **Настройка единого входа в Flatter Files** выполните следующие действия.<br><br>![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png) <br>

    а) Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. Войдите в приложение Flatter Files с правами администратора.

2. Щелкните Dashboard (Панель мониторинга). <br><br>![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png) <br>



2. Выберите элемент **Settings** (Параметры), а затем выполните следующие действия на вкладке **Company** (Компания). <br><br>![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png) <br>

    а) Установите флажок **Use SAML 2.0 For Authentication** (Использовать SAML 2.0 для проверки подлинности).

    б) Нажмите кнопку **Configure SAML** (Настроить SAML).



2. В диалоговом окне **SAML Configuration** (Настройка SAML) сделайте следующее. <br><br>![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png) <br>

    а) В поле Domain (Домен) укажите свой зарегистрированный домен.

    > [AZURE.NOTE] Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Flatter Files по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. На классическом портале Azure в диалоговом окне "Настройка единого входа в Flatter Files" скопируйте URL-адрес службы единого входа и вставьте его в текстовое поле "URL-адрес поставщика удостоверений".

    c. Создайте файл в кодировке **Base-64** из загруженного сертификата.

    >[AZURE.TIP] Подробности см. в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    г) Откройте сертификат в кодировке Base-64 в «Блокноте», скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений FlatterFiles**.

    д) Нажмите кнопку **Обновить**.

6. На классическом портале Azure AD выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите кнопку **Завершить**.

  
 
### Создание тестового пользователя Flatter Files

Цель этого раздела — создать пользователя с именем Britta Simon в Flatter Files.

**Как создать пользователя с именем Britta Simon в Flatter Files**

1. Войдите на сайт своей компании в службе **Flatter Files** с правами администратора.

2. На панели навигации слева щелкните пункт **Settings** (Параметры), а затем выберите вкладку **Users** (Пользователи). <br><br>![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)<br>

3. Нажмите кнопку **Add User** (Добавить пользователя).

4. На странице **Add User** (Добавление пользователя) выполните следующие действия. <br><br>![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)<br>

    а) В текстовом поле **Имя** введите **Britta**.

    б) В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Электронный адрес** введите электронный адрес пользователя Britta Simon на классическом портале Azure.

    c. Нажмите кнопку **Submit** (Отправить).


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив пользователю доступ к Flatter Files. <br><br>![Назначение пользователя][200] <br>

**Как назначить пользователя Britta Simon в Flatter Files**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>
2. Из списка приложений выберите **Flatter Files**. <br><br>![Назначение пользователя](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) <br>
1. В меню вверху щелкните пункт **Пользователи**. <br><br>![Назначение пользователя][203] <br>
1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкните элемент Flatter Files на панели доступа, чтобы автоматически войти в приложение Flatter Files.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0128_2016-->
<properties
	pageTitle="Руководство по интеграции Azure Active Directory с Litmos | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Litmos."
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
	ms.date="10/30/2015"
	ms.author="markusvi"/>


# Руководство по интеграции Azure Active Directory с Litmos

Цель этого руководства — показать, как интегрировать Litmos с Azure Active Directory (Azure AD).<br>Интеграция Litmos с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Litmos. 
- Вы можете включить автоматический вход пользователей в Litmos (единый вход) с учетной записью Azure AD.
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Litmos, вам потребуется:

- подписка Azure AD;
- подписка Litmos с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление Litmos из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление Litmos из коллекции
Чтобы настроить интеграцию Litmos с Azure AD, необходимо добавить Litmos из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Litmos из коллекции, выполните следующие действия.**

1. На **портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Litmos**.<br><br> ![Приложения][5]<br>
7. В области результатов выберите **Litmos** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Приложения][500]<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Litmos с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Litmos соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Litmos.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Litmos.
 
Чтобы настроить и проверить единый вход Azure AD в Litmos, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Litmos](#creating-a-halogen-software-test-user)** требуется для создания в Litmos пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение Litmos.<br> В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

В рамках конфигурации необходимо настроить **атрибуты маркера SAML** для приложения Litmos. <br><br> ![Единый вход в Azure AD][17] <br>

**Чтобы настроить единый вход Azure AD в Litmos, выполните следующие действия.**

1. На странице интеграции с приложением **Litmos** портала Azure AD щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Litmos?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Единый вход в Azure AD][7] <br>


1. Войдите на корпоративный сайт Litmos (например, **https://azureapptest.litmos.com/account/Login*) с правами администратора. <br><br> ![Единый вход в Azure AD][21] <br>


1. На панели навигации слева щелкните **Accounts** (Учетные записи). <br><br> ![Единый вход в Azure AD][22] <br>


1. Щелкните вкладку **Integrations** (Интеграции). <br><br> ![Единый вход в Azure AD][23] <br>


1. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**. <br><br> ![Единый вход в Azure AD][24] <br>

1. Скопируйте значение в разделе **The SAML endpoint for litmos is** (Конечная точка SAML для Litmos). <br><br> ![Единый вход в Azure AD][26] <br>


3. На портале Azure на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Единый вход в Azure AD][8] <br>
 
    а. В текстовом поле **Издатель** введите URL-адрес, используемый пользователями для входа в приложение Litmos (например, **https://azureapptest.litmos.com/account/Login*).
     
    b. В текстовое поле **URL-адрес ответа** вставьте значение, скопированное из приложения Litmos на предыдущем шаге.

    c. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Litmos** выполните следующие действия. <br><br>![Единый вход в Azure AD][2] <br>

    а. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.


1. В приложении **Litmos** выполните следующие действия. <br><br>![Единый вход в Azure AD][25] <br>

    а. Выберите команду **Enable SAML** (Включить SAML).

    b. Создайте файл в кодировке **Base-64** из загруженного сертификата.

    >[AZURE.TIP]Подробности см. в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    c. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **SAML X.509 Certificate** (Сертификат SAML X.509).

    г) Нажмите кнопку **Save Changes** (Сохранить изменения).


6. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]


20. В меню в верхней части экрана выберите пункт **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**. <br><br>![Настройка единого входа][12]<br>


24. В диалоговом окне **Добавление атрибута пользователя** выполните следующие действия. <br><br>![Настройка единого входа][14]<br>

    | Имя атрибута | Значение атрибута |
    | ---            | ---             |
    | Email | user.mail |
    | FirstName | user.givenname |
    | Lastname | user.surname |

    Для каждой строки данных в таблице выше выполните следующие действия.
   
    а. Щелкните **Добавить атрибут пользователя**. <br><br>![Настройка единого входа][15]<br>


    а. В текстовое поле **Имя атрибута** введите **имя атрибута**, отображаемое для этой строки.

    b. Выберите **значение атрибута**, отображаемое для этой строки.

    c. Щелкните **Завершить**, чтобы закрыть диалоговое окно **Добавление атрибута пользователя**.


25. Нажмите кнопку **Применить изменения**. <br><br>![Настройка единого входа][16]<br>




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.<br> В списке пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. В диалоговом окне на странице **Тип учетной записи пользователя** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br>

    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  В диалоговом окне на странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовое поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. В диалоговом окне на странице **Получение временного пароля** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя Litmos

Цель этого раздела — создать пользователя с именем Britta Simon в Litmos.<br> Приложение Litmos поддерживает JIT-подготовку. Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.

**Чтобы создать пользователя с именем Britta Simon в Litmos, выполните следующие действия.**


1. Войдите на корпоративный сайт Litmos (например, **https://azureapptest.litmos.com/account/Login*) с правами администратора. <br><br> ![Единый вход в Azure AD][21] <br>


1. На панели навигации слева щелкните **Accounts** (Учетные записи). <br><br> ![Единый вход в Azure AD][22] <br>


1. Щелкните вкладку **Integrations** (Интеграции). <br><br> ![Единый вход в Azure AD][23] <br>


1. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**. <br><br> ![Единый вход в Azure AD][24] <br>

1. Установите флажок **Autogenerate Users** (Автоматически создавать пользователей). <br><br> ![Единый вход в Azure AD][27] <br>


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Litmos. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Litmos, выполните следующие действия.**

1. Чтобы открыть представление приложений, на портале Azure в представлении каталога в меню вверху щелкните пункт **Приложения**. <br><br>![Назначение пользователя][201] <br>
2. В списке приложений выберите **Litmos**. <br><br>![Назначение пользователя][202] <br>
1. В меню вверху щелкните пункт **Пользователи**. <br><br>![Назначение пользователя][203] <br>
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Litmos на панели доступа, вы автоматически войдете в приложение Litmos.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=Nov15_HO2-->
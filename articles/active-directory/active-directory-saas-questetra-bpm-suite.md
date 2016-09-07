<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Questetra BPM Suite | Microsoft Aure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Questetra BPM Suite."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Questetra BPM Suite

Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Questetra BPM Suite. Интеграция Questetra BPM Suite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Questetra BPM Suite.
- Вы можете включить автоматический вход пользователей в Questetra BPM Suite (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Questetra BPM Suite, вам потребуется следующее:

- подписка Azure AD;
- Подписка [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) с включенным единым входом.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление Questetra BPM Suite из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Questetra BPM Suite из коллекции
Чтобы настроить интеграцию Questetra BPM Suite в Azure AD, необходимо добавить Questetra BPM Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Questetra BPM Suite из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Questetra BPM Suite**.

	![Приложения][5]

7. В области результатов выберите **Questetra BPM Suite** и нажмите кнопку **Завершить**, чтобы добавить приложение.



##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в Questetra BPM Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Questetra BPM Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Questetra BPM Suite. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Questetra BPM Suite.
 
Чтобы настроить и проверить единый вход Azure AD в Questetra BPM Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** требуется для создания соответствующего пользователя Britta Simon в Questetra BPM Suite, связанного с ее представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Questetra BPM Suite.

**Чтобы настроить единый вход Azure AD в Questetra BPM Suite, выполните следующие действия.**

1. На странице интеграции с приложением **Questetra BPM Suite** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][8]

2. На странице **Как пользователи должны входить в Questetra BPM Suite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][9]


3. В другом окне веб-браузера войдите в свой корпоративный веб-сайт **Questetra BPM Suite** в качестве администратора.

4. В верхнем меню щелкните пункт **Системные параметры**.

	![Единый вход в Azure AD][10]

5. Чтобы открыть страницу **SingleSignOnSAML**, щелкните **Единый вход (SAML)**.

	![Единый вход в Azure AD][11]


6. На диалоговой странице **Настройка параметров приложения** классического портала Azure выполните следующие действия:

	![Настройка параметров приложения][13]
 
    а. На веб-сайте компании **Questetra BPM Suite** в разделе "Сведения о SP" скопируйте **URL-адрес ACS** и вставьте его в текстовое поле **URL-адрес единого входа**.

    b. На веб-сайте компании **Questetra BPM Suite** в разделе «Сведения о SP» скопируйте **Идентификатор сущности** и вставьте его в текстовое поле **URL-адрес издателя**.

    в) На веб-сайте компании **Questetra BPM Suite** в разделе «Сведения о SP» скопируйте **URL-адрес ACS** и вставьте его в текстовое поле **URL-адрес ответа**.

    г) Нажмите кнопку **Далее**.

 
7. На странице **Настройка единого входа в Questetra BPM Suite** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на локальном компьютере.

	![Настройка единого входа][14]


8. На веб-сайте компании **Questetra BPM Suite** выполните следующие действия:

	![Настройка единого входа][15]

    а. Выберите пункт **Включить единый вход**.
     
    b. На классическом портале Azure скопируйте **URL-адрес издателя** и вставьте его в текстовое поле **Идентификатор сущности**.

    в) На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес страницы входа**.

    г) На классическом портале Azure скопируйте **URL-адрес службы единого выхода** и вставьте его в текстовое поле **URL-адрес страницы выхода**.

    д. В текстовом поле **Формат идентификатора имени** введите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.


    Е. Создайте файл в кодировке Base-64 из загруженного сертификата.

    >[AZURE.TIP] Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    g. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат проверки**.

    h. Щелкните **Сохранить**.


9. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	![Что такое Azure AD Connect?][17]


10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Что такое Azure AD Connect?][18]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD][100]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD][101]

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD][102]

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD][103]
 
    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
  
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку Далее.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD][104]
  
    а. В текстовом поле **Имя** введите **Britta**.
 
    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD][105]

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD][106]
  
    а. Запишите значение поля **Новый пароль**.
  
    b. Нажмите **Завершено**.
  
 
### Создание тестового пользователя Questetra BPM Suite

Цель этого раздела — создать пользователя с именем Britta Simon в Questetra BPM Suite.

**Чтобы создать пользователя с именем Britta Simon в Questetra BPM Suite, выполните следующие действия.**

1.	Выполните входа на сайт компании Questetra BPM Suite в качестве администратора.
2.	Последовательно выберите пункты **Системные параметры > Список пользователей > Новый пользователь**.
3.	В диалоговом окне создания нового пользователя выполните следующие действия:

	![Создание тестового пользователя][300]

    а. В текстовом поле **Имя** в Azure AD введите имя пользователя Britta.

    b. В текстовом поле **Электронная почта** в Azure AD введите адрес электронной почты пользователя Britta.

    c. В текстовом поле **Пароль** введите пароль.

4.	Нажмите кнопку **Добавить нового пользователя**.



### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ей доступ к Questetra BPM Suite.

![Что такое Azure AD Connect?][200]

**Чтобы назначить Britta Simon в Questetra BPM Suite, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.

	![Что такое Azure AD Connect?][201]

2. В списке приложений выберите **Questetra BPM Suite**.

	![Что такое Azure AD Connect?][205]

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Что такое Azure AD Connect?][202]

1. В списке пользователей выберите **Britta Simon**.

	![Что такое Azure AD Connect?][203]

2. На панели инструментов внизу щелкните **Назначить**.

	![Что такое Azure AD Connect?][204]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув элемент Questetra BPM Suite на панели доступа, вы автоматически войдете в приложение Questetra BPM Suite.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=AcomDC_0817_2016-->

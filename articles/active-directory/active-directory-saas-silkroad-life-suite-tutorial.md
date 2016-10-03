<properties
	pageTitle="Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite | Microsoft Azure"
	description="Узнайте, как настроить единый вход для Azure Active Directory и SilkRoad Life Suite."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением SilkRoad Life Suite. Интеграция SilkRoad Life Suite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к SilkRoad Life Suite.
- Вы можете включить автоматический вход пользователей в SilkRoad Life Suite (единый вход) с учетной записью Azure AD.

Подробные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с SilkRoad Life Suite, вам потребуется следующее:

- подписка Azure AD;
- подписка SilkRoad Life Suite с активированной функцией единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SilkRoad Life Suite из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление SilkRoad Life Suite из коллекции.
Чтобы настроить интеграцию SilkRoad Life Suite в Azure AD, необходимо добавить SilkRoad Life Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SilkRoad Life Suite из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **SilkRoad Life Suite**.

	![Приложения][5]

7. В области результатов выберите **SilkRoad Life Suite** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Приложения][50]


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в SilkRoad Life Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SilkRoad Life Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SilkRoad Life Suite. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SilkRoad Life Suite.
 
Чтобы настроить и проверить единый вход Azure AD в SilkRoad Life Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** требуется для создания пользователя Britta Simon в SilkRoad Life Suite, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход в Azure AD на классическом портале Azure и настроить единый вход в приложение SilkRoad Life Suite.

**Чтобы настроить единый вход в Azure AD в SilkRoad Life Suite, выполните следующие действия:**

5. Войдите на сайт компании SilkRoad от имени администратора.


    > [AZURE.NOTE] Чтобы получить доступ к приложению SilkRoad Life Suite Authentication для настройки федерации с Microsoft Azure AD, обратитесь в службу поддержки компании SilkRoad или к представителю отдела обслуживания клиентов компании SilkRoad.


6. В разделе **Service Provider** (Поставщик услуг) щелкните **Federation Details** (Сведения о федерации).

	![Единый вход в Azure AD][10]


1. Щелкните **Download Federation Metadata** (Загрузить метаданные федерации), а затем сохраните файл метаданных на вашем компьютере.

	![Единый вход в Azure AD][11]

3. На классическом портале Azure на странице интеграции с приложением **SilkRoad Life Suite** нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в SilkRoad Life Suite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][7]

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Единый вход в Azure AD][8]
 
    а. В текстовое поле **URL-адрес для входа** введите URL-адрес, используемый пользователями для входа на сайт SilkRoad Life Suite (например, *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. Откройте скачанный файл метаданных **Silkroad**.

    в) Найдите тег **AssertionConsumerService**, а затем скопируйте атрибут **Location**.

	![Единый вход в Azure AD][21]
   
    г) Вставьте значение в текстовое поле **URL-адрес ответа**.
 
    д. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в SilkRoad Life Suite** выполните следующие действия:

	![Единый вход в Azure AD][9]

    а. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.




1. В приложении **SilkRoad** щелкните **Authentication Sources** (Источники аутентификации).

	![Единый вход в Azure AD][12]



1. Щелкните **Add Authentication Source** (Добавить источник аутентификации).

	![Единый вход в Azure AD][13]



1. В разделе **Add Authentication Source** (Добавление источника аутентификации) выполните следующие действия:

	![Единый вход в Azure AD][14]

    а. В разделе **Вариант 2 — файл метаданных** нажмите кнопку **Обзор**, чтобы отправить скачанный файл метаданных.

    b. Нажмите кнопку **Создать поставщик удостоверений с помощью данных из файла**.



1. В разделе **Authentication Sources** (Источники аутентификации) нажмите кнопку **Edit** (Изменить).

	![Единый вход в Azure AD][15]


1. В диалоговом окне **Edit Authentication Source** (Изменение источника аутентификации) выполните следующие действия:

	![Единый вход в Azure AD][16]

    а. В поле **Enabled** (Включено) выберите **Yes** (Да).

    b. В текстовом поле **Описание IdP** введите описание вашей конфигурации (например, *Azure AD SSO*).

    в) В текстовом поле **Имя IdP** введите уникальное имя вашей конфигурации (например, *Azure SP*).

    г) Щелкните **Сохранить**.


6. Отключите все остальные источники аутентификации.

	![Единый вход в Azure AD][17]

7. На классическом портале Azure на странице **Подтверждение единого входа** щелкните **Далее**.

	![Единый вход в Azure AD][18]

1. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Единый вход в Azure AD][19]


### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png)
 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя SilkRoad Life Suite

Цель этого раздела — создать в SilkRoad Life Suite пользователя с именем Britta Simon. Пользователь Britta должен иметь идентификатор единого входа (иногда называемый *AuthParam*), который соответствует **адресу электронной почты** этого пользователя в Azure AD.

**Чтобы создать пользователя с именем Britta Simon в SilkRoad Life Suite, выполните следующие действия:**

1. Попросите службу поддержки SilkRoad Life Suite создать пользователя, у которого значение атрибута **SSO ID** совпадает с **адресом электронной почты** пользователя Britta Simon в Azure AD.



### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure путем предоставления доступа к SilkRoad Life Suite.

![Назначение пользователя][200]

**Чтобы назначить Britta Simon в SilkRoad Life Suite, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **SilkRoad Life Suite**.

	![Назначение пользователя][202]

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув плитку SilkRoad Life Suite на панели доступа, вы автоматически войдете в приложение SilkRoad Life Suite.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->
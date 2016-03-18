<properties
	pageTitle="Руководство. Интеграция Azure Active Directory и StatusPage | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и StatusPage."
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
	ms.date="02/02/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory и StatusPage

Цель этого руководства — показать, как интегрировать приложение StatusPage с Azure Active Directory (Azure AD).<br>Интеграция StatusPage с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению StatusPage. 
- Вы можете включить автоматический вход пользователей в StatusPage (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с приложением StatusPage, вам потребуются следующие компоненты:

- подписка Azure AD;
- подписка StatusPage с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление StatusPage из коллекции. 
2. Настройка и проверка единого входа в Azure AD


## Добавление StatusPage из коллекции
Чтобы настроить интеграцию приложения StatusPage с Azure AD, вам нужно добавить StatusPage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить StatusPage из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **StatusPage**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)<br>
7. В области результатов выберите **StatusPage** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении StatusPage с использованием тестового пользователя Britta Simon.

Для включения единого входа в Azure AD необходимо знать, какой пользователь в приложении StatusPage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в StatusPage.<br> Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в StatusPage.
 
Чтобы настроить и проверить единый вход Azure AD в приложении StatusPage, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя StatusPage](#creating-a-statuspage-test-user)** требуется для создания в StatusPage соответствующего пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение StatusPage.



**Чтобы настроить единый вход Azure AD в StatusPage, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **StatusPage** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в StatusPage?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) <br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) <br>

    > [AZURE.NOTE] Обратитесь в службу поддержки StatusPage по адресу [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io), чтобы запросить метаданные, необходимые для настройки единого входа.


    а. В метаданных скопируйте значение издателя и вставьте его в текстовое поле **Идентификатор**.

    b. В полученных метаданных скопируйте значение URL-адреса ответа и вставьте его в текстовое поле **URL-адрес ответа**.

    c. Нажмите кнопку **Далее**.
 
 
4. На странице **Настройка единого входа в StatusPage** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. В другом окне браузера войдите на корпоративный сайт StatusPage с правами администратора.

1. На главной панели инструментов щелкните **Manage Account** (Управление учетной записью). <br><br> ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) <br>


1. Перейдите на вкладку **Single Sign-on** (Единый вход). <br><br> ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) <br>


1. На странице настройки единого входа выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) <br>

    а. На классическом портале Azure на странице диалогового окна **Настройка единого входа в StatusPage** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Целевой URL-адрес для единого входа**.

    b. Откройте скачанный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Certificate** (Сертификат).

    c. Щелкните **Сохранить**.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя в приложении StatusPage

Цель этого раздела — создать в приложении StatusPage пользователя с именем Britta Simon. Приложение StatusPage поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).


**Чтобы создать в приложении StatusPage пользователя с именем Britta Simon, выполните следующие действия.**

1. Войдите на корпоративный сайт StatusPage с правами администратора.

1. В меню вверху щелкните **Manage Account** (Управление учетной записью).

1. Перейдите на вкладку «Team Members» (Участники команды). <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) <br>

1. Щелкните **Add Team Member** (Добавить участника команды). <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) <br>

1. Введите в текстовые поля **Email Address** (Адрес электронной почты), **First Name** (Имя) и **Sur Name** (Фамилия) соответствующие данные действительного пользователя, которого вы хотите подготовить. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) <br>

1. Для параметра **Role** (Роль) выберите значение **Client Administrator** (Администратор клиента).

1. Щелкните **Create Account** (Создать учетную запись).

### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к StatusPage. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon приложению StatusPage, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **StatusPage**. <br><br>![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) <br>

1. В меню вверху щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент StatusPage на панели доступа, вы автоматически войдете в приложение StatusPage.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->
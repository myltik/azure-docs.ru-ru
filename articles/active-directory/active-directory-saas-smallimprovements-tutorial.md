<properties
	pageTitle="Учебник. Интеграция Azure Active Directory со Small Improvements | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Small Improvements."
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
	ms.date="12/14/2015"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory со Small Improvements

Цель этого руководства — показать, как интегрировать Small Improvements с Azure Active Directory (Azure AD).<br>Интеграция Small Improvements с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению Small Improvements.
- Вы можете включить автоматический вход пользователей в Small Improvements (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD со Small Improvements, вам потребуется:

- подписка Azure AD;
- подписка Small Improvements с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Small Improvements из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Small Improvements из коллекции
Чтобы настроить интеграцию Small Improvements с Azure AD, необходимо добавить Small Improvements из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Small Improvements из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Small Improvements**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)<br>
7. В области результатов выберите **Small Improvements** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Small Improvements с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Small Improvements соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Small Improvements.<br> Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Small Improvements.

Чтобы настроить и проверить единый вход Azure AD в Small Improvements, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Small Improvements](#creating-a-small-improvements-test-user)** требуется для создания пользователя Britta Simon в Small Improvements, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Small Improvements.



**Чтобы настроить единый вход Azure AD в Small Improvements, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Small Improvements** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Small Improvements?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br>![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png)<br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) <br>

    > [AZURE.NOTE]Обратитесь в службу поддержки Small Improvements по адресу [Support@small-improvements.com](mailto:support@small-improvements.com), чтобы настроить доменное имя для своей учетной записи. Без него единый вход работать не будет.


    а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Small Improvements. b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в Small Improvements** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) <br>

    а. Щелкните **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. В другом окне браузера войдите на корпоративный сайт Small Improvements с правами администратора.

1. На главной странице панели мониторинга нажмите слева кнопку **Администрирование**. <br><br> ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) <br>


1. Нажмите кнопку **Единый вход SAML** в разделе **Интеграции**. <br><br> ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png)<br>


1. На странице настройки единого входа выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png) <br>

    а. На странице диалогового окна **Настройка единого входа в Small Improvements** портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Конечная точка HTTP**.

    b. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509**.

    c. Если вы хотите предоставить пользователям единый вход и возможность проверки подлинности через форму входа, установите флажок **Включить также доступ по имени входа и паролю**.
  
	г) Введите значение имени единого входа в текстовое поле **Приглашение SAML**.

	д. Щелкните **Сохранить**.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) <br>

5. В диалоговом окне на странице **Тип учетной записи пользователя** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  В диалоговом окне на странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png) <br>

    а. В текстовое поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. е. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) <br>

8. В диалоговом окне на странице **Получение временного пароля** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Small Improvements

Цель этого раздела — создать в приложении Small Improvements пользователя с именем Britta Simon. Эта функция уже включена в ходе [настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).


**Чтобы создать в приложении Small Improvements пользователя с именем Britta Simon, выполните следующие действия.**

1. Войдите на корпоративный сайт Small Improvements с правами администратора.

1. На главной странице перейдите к меню в левой части страницы и щелкните **Администрирование**.

1. Нажмите кнопку **Каталог пользователей** в разделе "Управление пользователями". <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) <br>

1. Щелкните **Добавить члена команды**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) <br>

1. Заполните текстовые поля **Имя**, **Фамилия** и **Адрес электронной почты** данными действительного пользователя, которого вы хотите подготовить. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) <br>

1. Вы также можете ввести персональное сообщение в поле **Отправить уведомление по электронной почте**. Если уведомление отправлять не нужно, снимите этот флажок.

1. Щелкните **Создать пользователей**.

### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Small Improvements. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Small Improvements, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **Small Improvements**. <br><br>![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) <br>

1. В меню вверху щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Small Improvements на панели доступа, вы автоматически войдете в приложение Small Improvements.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1217_2015-->
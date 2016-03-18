<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Mixpanel | Microsoft Azure"
	description="Сведения о настройке единого входа Azure Active Directory в Mixpanel."
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


# Руководство. Интеграция Azure Active Directory с Mixpanel

Цель этого руководства — показать, как интегрировать Mixpanel с Azure Active Directory (Azure AD).<br>Интеграция Mixpanel с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Mixpanel.
- Вы можете включить автоматический вход пользователей в Mixpanel (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Mixpanel, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа Mixpanel


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Mixpanel из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Mixpanel из коллекции
Чтобы настроить интеграцию Mixpanel с Azure AD, необходимо добавить Mixpanel из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mixpanel из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Mixpanel**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_01.png)<br>
7. В области результатов выберите **Mixpanel** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Mixpanel с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Mixpanel соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mixpanel.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mixpanel.

Чтобы настроить и проверить единый вход Azure AD в Mixpanel, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Mixpanel](#creating-a-mixpanel-test-user)** требуется для создания в Mixpanel пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Mixpanel.



**Чтобы настроить единый вход Azure AD в Mixpanel, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Mixpanel** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Mixpanel?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br>![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_03.png)<br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_04.png) <br>


    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Mixpanel по следующей схеме: **"https://mixpanel.com/login/"**.

    > [AZURE.NOTE] Зарегистрируйтесь на сайте [https://mixpanel.com/register/](https://mixpanel.com/register/), настройте учетные данные для входа в систему и обратитесь в [службу поддержки Mixpanel](mailto:support@Mixpanel.com), чтобы включить параметры единого входа для вашего клиента. <br>При необходимости обратитесь за URL-адресом единого входа в службу поддержки Mixpanel.

    b. Нажмите кнопку **Далее**.



4. На странице **Настройка единого входа в Mixpanel** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_05.png) <br>

    а. Щелкните **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. В отдельном окне браузера войдите в приложение Mixpanel под учетной записью администратора.
   
6. Щелкните маленький значок **шестеренки** в левом нижнем углу страницы. <br><br>![Единый вход в Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) <br>

7. Откройте вкладку **Безопасность доступа** и щелкните **Изменить параметры**. <br><br>![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) <br>
    
8. На диалоговой странице **Изменение сертификата** нажмите кнопку **Выбрать файл**, чтобы отправить загруженный сертификат, а затем кнопку **Далее**. <br><br>![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) <br>

9. На диалоговой странице **Настройка единого входа в Mixpanel** классического портала Azure скопируйте значение **URL-адрес службы единого входа**, вставьте его в текстовое поле URL-адреса проверки подлинности на диалоговой странице **Изменение URL-адреса проверки подлинности** и нажмите кнопку **Далее**. <br><br>![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) <br>
    
1. Нажмите кнопку **Done** (Готово).


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Mixpanel

Цель этого раздела — создать пользователя с именем Britta Simon в Mixpanel.

1.	Выполните вход на веб-сайт компании Mixpanel в качестве администратора.

2.	Щелкните маленькую кнопку с шестеренкой в левом нижнем углу страницы, чтобы открыть окно **Параметры**.

3.	Откройте вкладку **Команды**.

4. В текстовое поле **член команды** введите электронный адрес пользователя Britta Simon на портале Azure. <br><br>![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) <br>

4.	Нажмите кнопку **Пригласить**.

Пользователь получит по электронной почте письмо с предложением настроить свой профиль.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Mixpanel. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Mixpanel, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **Mixpanel**. <br><br>![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_50.png) <br>

1. В меню вверху щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Mixpanel на панели доступа, вы автоматически войдете в приложение Mixpanel.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0204_2016-->
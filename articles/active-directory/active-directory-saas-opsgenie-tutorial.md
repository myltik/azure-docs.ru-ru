<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с OpsGenie | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и OpsGenie."
	services="active-directory"
	documentationCenter=""
	authors="ashimaabrol"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="v-aabrol"/>


# Руководство. Интеграция Azure Active Directory с OpsGenie

Цель этого руководства — показать, как интегрировать OpsGenie с Azure Active Directory (Azure AD).<br>Интеграция OpsGenie с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к OpsGenie.
- Вы можете включить автоматический вход пользователей в OpsGenie (единый вход) под учетной записью Azure AD.
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с OpsGenie, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа OpsGenie.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление OpsGenie из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление OpsGenie из коллекции
Чтобы настроить интеграцию OpsGenie с Azure AD, необходимо добавить OpsGenie из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OpsGenie из коллекции, выполните следующие действия.**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **OpsGenie**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)<br>
7. В области результатов выберите **OpsGenie** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в OpsGenie с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в OpsGenie соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpsGenie.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OpsGenie.

Чтобы настроить и проверить единый вход Azure AD в OpsGenie, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя OpsGenie](#creating-a-opsgenie-test-user)** требуется для создания пользователя Britta Simon в OpsGenie, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение OpsGenie.



**Чтобы настроить единый вход Azure AD в OpsGenie, выполните следующие действия.**

1. На странице интеграции с приложением **OpsGenie** портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в OpsGenie?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br>![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png)<br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) <br>


    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение OpsGenie по следующей схеме: **https://app.opsgenie.com/auth/login**.

    > [AZURE.NOTE]Если вам нужен URL-адрес единого входа, обратитесь в [службу поддержки OpsGenie](mailto:support@opsgenie.com).

    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в OpsGenie** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) <br>

    а. Щелкните **Загрузить сертификат** и сохраните файл сертификата на свой компьютер. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на стороне OpsGenie.

    b. Нажмите кнопку **Далее**.


5. Откройте другое окно браузера и войдите в OpsGenie с правами администратора.

6. Щелкните **Параметры** и откройте вкладку **Единый вход**. <br><br>![Единый вход в OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) <br>

7. Чтобы включить единый вход, установите флажок **Включено**. <br><br>![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) <br>
   
8. В разделе **Поставщик** откройте вкладку **Azure Active Directory**. <br><br>![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) <br>
    
9. На странице диалогового окна Azure Active Directory выполните следующие действия. <br><br>![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) <br>

    а. На странице диалогового окна **Настройка единого входа в OpsGenie** портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Конечная точка SAML 2.0**.

    b. Создайте файл в кодировке Base-64 из загруженного сертификата.
    
    > [AZURE.NOTE]Дополнительные сведения вы можете узнать в видео [Преобразование двоичного сертификата в текстовый файл](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.500**.

    г) Нажмите кнопку **Сохранить изменения**.


6. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.<br> В списке пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя OpsGenie

Цель этого раздела — создать пользователя с именем Britta Simon в OpsGenie.

1.	В окне веб-браузера войдите в клиент OpsGenie с правами администратора.

2.	Перейдите к списку пользователей, щелкнув **Пользователь** на левой панели. <br><br>![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) <br>

3.	Нажмите кнопку **Добавить пользователя**.

3.	На странице **Добавление пользователя** выполните следующие действия. <br><br>![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) <br>

    а. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя Britta Simon в Azure Active Directory.

    b. В текстовом поле **Полное имя** введите **Britta Simon**.

    c. Щелкните **Сохранить**.

Britta получит по электронной почте инструкции по настройке профиля.


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к OpsGenie. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в OpsGenie, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **OpsGenie**. <br><br>![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув элемент OpsGenie на панели доступа, вы автоматически войдете в приложение OpsGenie.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1223_2015-->
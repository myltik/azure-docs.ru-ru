<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с SD Elements | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и SD Elements."
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
	ms.date="03/08/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с SD Elements

Цель этого учебника — показать, как интегрировать SD Elements с Azure Active Directory (Azure AD).<br>Интеграция SD Elements с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению SD Elements.
- Вы можете включить автоматический вход пользователей в SD Elements (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через Azure Active Directory. 


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с SD Elements, вам потребуется:

- подписка Azure AD;
- подписка на SD Elements с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SD Elements из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление SD Elements из коллекции.
Чтобы настроить интеграцию SD Elements с Azure AD, необходимо добавить SD Elements из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SD Elements из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **SD Elements**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)<br>
7. В области результатов выберите **SD Elements** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в SD Elements с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SD Elements соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в SD Elements.<br> Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SD Elements.

Чтобы настроить и проверить единый вход Azure AD в SD Elements, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя SD Elements](#creating-a-sd-elements-test-user)** требуется для создания пользователя Britta Simon в SD Elements, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение SD Elements.

Приложение SD Elements ожидает проверочных утверждений SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML**. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) <br>



**Чтобы настроить единый вход Azure AD в SD Elements, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **SD Elements** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в SD Elements?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) <br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) <br>


    а. В текстовое поле **Издатель** введите URL-адрес издателя клиента в следующем формате: *https://\<имя вашего клиента>.sdelements.com/sso/saml2/metadata*.
   
    b. В текстовое поле **URL-адрес ответа** введите URL-адрес ответа клиента в следующем формате: *https://\<имя вашего клиента>.sdelements.com/sso/saml2/acs/*.

    > [AZURE.NOTE] Если вам нужен действительный URL-адрес и URL-адрес ответа вашего клиента, обратитесь в [службу поддержки SD Elements](mailto:support@sdelements.com).
      
    c. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в SD Elements** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. Чтобы включить единый вход, обратитесь в [службу поддержки SD Elements](mailto:support@sdelements.com) и предоставьте загруженный файл сертификата.


5. В отдельном окне браузера войдите в клиент SD Elements под учетной записью администратора.

6. В верхнем меню щелкните пункт "Система", а затем "Единый вход". <br><br>![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) <br>


7. В диалоговом окне **Параметры единого входа** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) <br>

    а. Для параметра **Тип SSO** выберите значение **SAML**.

    b. На странице диалогового окна **Настройка единого входа в SD Elements** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Идентификатор сущности поставщика удостоверений**.

    c. На странице диалогового окна **Настройка единого входа в SD Elements** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Служба единого входа поставщика удостоверений**.

    d. Щелкните **Сохранить**.

6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]

1. В меню в верхней части экрана выберите пункт **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**. <br><br>![Настройка единого входа][21]<br>


2. Для каждой строки в приведенной ниже таблице выполните следующие действия.

    | Имя атрибута | Значение атрибута |
    | ---            | ---             |
    | email | user.mail |
    | firstname | user.givenname |
    | lastname | user.surname |


    а. Щелкните **Добавить атрибут пользователя**. <br><br>![Настройка единого входа][23]<br>

    b. В текстовом поле **Имя атрибута** введите **имя атрибута**, а в поле **Значение атрибута** выберите значение атрибута, указанное в этой строке.<br><br>![Настройка единого входа][22]<br>

    c. Щелкните **Добавить атрибут пользователя**. <br><br>![Настройка единого входа][23]<br>

1. Нажмите кнопку **Применить изменения**. <br><br>![Настройка единого входа][24]<br>

### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя SD Elements

Цель этого раздела — создать в приложении SD Elements пользователя с именем Britta Simon. Пользователи SD Elements создаются вручную.

**Чтобы создать пользователя с именем Britta Simon в SD Elements, выполните следующие действия.**

1.	Откройте браузер и войдите на корпоративный сайт SD Elements с правами администратора.

2.	На панели инструментов вверху щелкните "Управление пользователями", а затем "Пользователи".
 
    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) <br>

3.	Нажмите кнопку "Добавить нового пользователя".
 
    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) <br>

4.	В диалоговом окне добавления нового пользователя выполните следующие действия.

    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) <br>

    а. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя Britta Simon в Azure AD.

    b. В текстовом поле **Имя** введите **Britta**.

    c. В текстовом поле **Фамилия** введите **Simon**.

    d. Для параметра **Роль** выберите значение **Пользователь**.

    д. Нажмите кнопку **Создать пользователя**.




### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SD Elements. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в SD Elements, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **SD Elements**. <br><br>![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. В списке **Пользователи** выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув элемент SD Elements на панели доступа, вы автоматически войдете в приложение SD Elements.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0309_2016-->
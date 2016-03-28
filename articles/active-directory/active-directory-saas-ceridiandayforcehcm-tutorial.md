<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Ceridian Dayforce HCM | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Ceridian Dayforce HCM."
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
	ms.date="03/14/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Ceridian Dayforce HCM

Цель этого учебника — показать, как интегрировать Ceridian Dayforce HCM с Azure Active Directory (Azure AD).<br>Интеграция Ceridian Dayforce HCM с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Ceridian Dayforce HCM.
- Вы можете включить автоматический вход пользователей в Ceridian Dayforce HCM (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Ceridian Dayforce HCM, вам потребуется:

- подписка Azure AD;
- подписка на Ceridian Dayforce HCM с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Ceridian Dayforce HCM из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Ceridian Dayforce HCM из коллекции
Чтобы настроить интеграцию Ceridian Dayforce HCM с Azure AD, необходимо добавить Ceridian Dayforce HCM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ceridian Dayforce HCM из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Ceridian Dayforce HCM**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)<br>
7. В области результатов выберите **Ceridian Dayforce HCM** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Ceridian Dayforce HCM с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Ceridian Dayforce HCM соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ceridian Dayforce HCM.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Ceridian Dayforce HCM.

Чтобы настроить и проверить единый вход Azure AD в Ceridian Dayforce HCM, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Ceridian Dayforce HCM](#creating-a-ceridiandayforcehcm-test-user)** требуется для создания пользователя Britta Simon в Ceridian Dayforce HCM, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении Ceridian Dayforce HCM.

Приложение Ceridian Dayforce HCM ожидает утверждения SAML в определенном формате. Обратитесь в службу поддержки Dayforce HCM, чтобы определить правильный идентификатор пользователя, который будет сопоставляться в приложении. Вы выполните рекомендации от службы поддержки Dayforce HCM, касающиеся атрибута, который она хочет использовать для данного сопоставления. Корпорация Майкрософт рекомендует использовать атрибут **"name"** в качестве идентификатора пользователя. Управлять значением этого атрибута можно на вкладке **Атрибут** приложения. На следующем снимке экрана приведен пример. Утверждение имени было сопоставлено с атрибутом Extension **extensionattribute2**, имеющим уникальный идентификатор сотрудника, который будет отправляться в приложение Dayforce HCM в каждом успешном ответе SAML.

<br> ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) <br>

**Чтобы настроить единый вход Azure AD в Ceridian Dayforce HCM, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Ceridian Dayforce HCM** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Ceridian Dayforce HCM?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) <br>

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) <br>


    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение Ceridian Dayforce HCM по следующей схеме: **https://</INSTANCENAME/>.dayforcehcm.com/</COMPANYNAME/>**.

	b. В текстовом поле "URL-адрес ответа" введите URL-адрес, где Azure AD разместит ответ. Точный URL-адрес экземпляра можно получить в службе поддержки Ceridian Dayforce. Он должен иметь следующий вид: **"https://</SERVERNAME/>.dayforcehcm.com/sp/ACS.saml2"**.

4. На странице **Настройка единого входа в Ceridian Dayforce HCM** выполните следующие действия: <br><br>![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы получить данные единого входа, настроенные для вашего приложения, обратитесь в службу поддержки Ceridian Dayforce HCM и отправьте по электронной почте скачанный файл сертификата. Также укажите URL-адрес издателя, URL-адрес единого входа SAML и URL-адрес службы выхода, чтобы настроить их для интеграции единого входа.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Ceridian Dayforce HCM

Цель этого раздела — создать пользователя с именем Britta Simon в Ceridian Dayforce HCM. Обратитесь в службу поддержки Ceridian Dayforce HCM, чтобы добавить пользователей в учетную запись Ceridian Dayforce HCM.


> [AZURE.NOTE] Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Ceridian Dayforce HCM.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Ceridian Dayforce HCM. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Ceridian Dayforce HCM, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **Ceridian Dayforce HCM**. <br><br>![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png)<br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Ceridian Dayforce HCM на панели доступа, вы автоматически войдете в приложение Ceridian Dayforce HCM.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0316_2016-->
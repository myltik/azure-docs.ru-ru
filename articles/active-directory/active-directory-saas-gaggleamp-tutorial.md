<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с GaggleAMP | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в приложении GaggleAMP."
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
	ms.date="12/14/2015"
	ms.author="v-aabrol"/>


# Руководство. Интеграция Azure Active Directory с GaggleAMP

Цель этого руководства — показать, как интегрировать GaggleAMP с Azure Active Directory (Azure AD). <br>Интеграция GaggleAMP с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к приложению GaggleAMP.
- Вы можете включить автоматический вход пользователей в GaggleAMP (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с GaggleAMP, вам потребуется:

- подписка Azure AD;
- подписка на GaggleAMP с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление GaggleAMP из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление GaggleAMP из коллекции
Чтобы настроить интеграцию GaggleAMP с Azure AD, необходимо добавить GaggleAMP из коллекции в список управляемых приложений SaaS.

**Чтобы добавить GaggleAMP из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **GaggleAMP**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_01.png)<br>
7. В области результатов выберите **GaggleAMP** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в GaggleAMP с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в GaggleAMP соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в GaggleAMP.<br> Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в GaggleAMP.

Чтобы настроить и проверить единый вход Azure AD в GaggleAMP, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя GaggleAMP](#creating-a-GaggleAMP-test-user)** требуется для создания в GaggleAMP пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение GaggleAMP.



**Чтобы настроить единый вход Azure AD в GaggleAMP, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **GaggleAMP** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в GaggleAMP?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br>![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_03.png)<br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_04.png) <br>


    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение GaggleAMP по следующей схеме: **"https://secure4.gaggleamp.com"**.

    > [AZURE.NOTE]Если вам нужен **URL единого входа** для приложения, обратитесь в [отдел продаж GaggleAMP](mailto:sales@gaggleamp.com).

    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в GaggleAMP** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_05.png) <br>

    а. Щелкните **Загрузить сертификат** и сохраните файл сертификата на свой компьютер. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на стороне GaggleAMP.

    b. Нажмите кнопку **Далее**.


5. В другом окне браузера откройте страницу единого входа SAML, созданную для вас службой поддержки Gaggle (например **https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).

6. На странице **Единый вход SAML** выполните следующие действия. <br><br>![Единый вход в GaggleAMP](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_06.png) <br>

    а. На классическом портале Azure скопируйте **URL-адрес издателя** и вставьте его в текстовое поле **Издатель поставщика удостоверений**.

    b. На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес единого входа поставщика удостоверений**.

    c. Нажмите кнопку **Сохранить**.
    
    г) Отправить загруженный сертификат в [отдел продаж GaggleAMP](mailto:sales@gaggleamp.com).


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_04.png) <br>

5. В диалоговом окне на странице **Тип учетной записи пользователя** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  В диалоговом окне на странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_06.png) <br>

    а. В текстовое поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_07.png) <br>

8. В диалоговом окне на странице **Получение временного пароля** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение в поле **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя GaggleAMP

Цель этого раздела — создать в приложении GaggleAMP пользователя с именем Britta Simon. Приложение GaggleAMP поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к GaggleAMP (если он еще не создан).


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к GaggleAMP. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в GaggleAMP, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. Из списка приложений выберите **GaggleAMP**. <br><br>![Список Azure](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_50.png)<br>

1. В меню вверху щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент GaggleAMP на панели доступа, вы автоматически войдете в приложение GaggleAMP.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1217_2015-->
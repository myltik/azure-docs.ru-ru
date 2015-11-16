<properties
	pageTitle="Руководство по интеграции Azure Active Directory с Promapp | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Promapp."
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
	ms.date="11/03/2015"
	ms.author="markusvi"/>


# Руководство по интеграции Azure Active Directory с Promapp

Цель этого руководства — показать, как интегрировать Promapp с Azure Active Directory (Azure AD).<br>Интеграция Promapp с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Promapp. 
- Вы можете включить автоматический вход пользователей в Promapp (единый вход) с использованием учетной записи Azure AD.
- вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Promapp, вам потребуется:

- подписка Azure AD;
- подписка Promapp с поддержкой единого входа.


> [AZURE.NOTE]Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление Promapp из коллекции. 
2. Настройка и проверка единого входа в Azure AD


## Добавление Promapp из коллекции
Чтобы настроить интеграцию Promapp с Azure AD, необходимо добавить Promapp из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Promapp из коллекции, выполните следующие действия.**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Promapp**.<br><br> ![Приложения][5]<br>
7. В области результатов выберите **Promapp** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Приложения][500]<br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Promapp с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Promapp соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Promapp.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Promapp.
 
Чтобы настроить и проверить единый вход Azure AD в Promapp, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Promapp](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в Promapp, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложение Promapp.<br>

**Чтобы настроить единый вход Azure AD в Promapp, выполните следующие действия.**

1. На портале Azure AD на странице интеграции с приложением **Promapp** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Promapp?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Единый вход в Azure AD][7] <br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Единый вход в Azure AD][8] <br>
 
     а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт Promapp (например, **https://companyname.promapp.com/instancename*).


     b. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Promapp** выполните следующие действия. <br><br>![Единый вход в Azure AD][9] <br>

    а. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


6. Войдите на корпоративный сайт Promapp с правами администратора.

6. В меню вверху щелкните **Admin** (Администратор). <br><br>![Единый вход в Azure AD][12]<br>

6. Щелкните **Configure** (Настроить). <br><br>![Единый вход в Azure AD][13]<br>



4. В диалоговом окне **Security** (Безопасность) сделайте следующее. <br><br>![Единый вход в Azure AD][14] <br>

    а. На портале Azure в диалоговом окне **Настройка единого входа в Promapp** скопируйте значение поля **URL-адрес удаленного входа**, вставьте его в текстовое поле **SSO-Login URL** (URL-адрес входа) и нажмите кнопку **Save** (Сохранить).

    b. В поле **SSO - Single Sign-on Mode** (SSO — режим единого входа) выберите **Optional** (Необязательно), а затем нажмите кнопку **Save** (Сохранить).

    c. Откройте загруженный сертификат в блокноте, скопируйте содержимое сертификата без первой строки (*-----BEGIN CERTIFICATE-----*) и последней строки (*-----END CERTIFICATE-----*), вставьте его в текстовое поле **SSO-x.509 Certificate** (Сертификат единого входа x.509) и нажмите кнопку **Save** (Сохранить).




6. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

  <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.<br> В списке пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) <br>

5. В диалоговом окне на странице **Тип учетной записи пользователя** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  В диалоговом окне на странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовое поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) <br>
 
8. В диалоговом окне на странице **Получение временного пароля** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя Promapp

Приложение Promapp поддерживает JIT-подготовку. Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Promapp. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Promapp, выполните следующие действия.**

1. Чтобы открыть представление приложений, на портале Azure в представлении каталога в меню вверху щелкните пункт **Приложения**. <br><br>![Назначение пользователя][201] <br>
2. В списке приложений выберите **Promapp**. <br><br>![Назначение пользователя][202] <br>
1. В меню вверху щелкните пункт **Пользователи**. <br><br>![Назначение пользователя][203] <br>
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув плитку Promapp на панели доступа, вы автоматически войдете в приложение Promapp.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png

<!---HONumber=Nov15_HO2-->
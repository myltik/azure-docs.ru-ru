.<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с MOVEit Transfer | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в MOVEit Transfer."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

.<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с MOVEit Transfer

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением MOVEit Transfer.

Интеграция MOVEit Transfer с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к MOVEit Transfer.
- Вы можете включить автоматический вход пользователей в MOVEit Transfer (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с MOVEit Transfer, вам потребуется:

- подписка Azure AD;
- подписка MOVEit Transfer с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление MOVEit Transfer из коллекции.
2. Настройка и проверка единого входа в Azure AD


## Добавление MOVEit Transfer из коллекции
Чтобы настроить интеграцию MOVEit Transfer с Azure AD, необходимо добавить MOVEit Transfer из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MOVEit Transfer из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
	
	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.
	
	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	.![Приложения][4]

6. В поле поиска введите **MOVEit Transfer**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)

7. В области результатов выберите **MOVEit Transfer** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	.![Выбор приложения в коллекции](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложение MOVEit Transfer с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в MOVEit Transfer соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MOVEit Transfer.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в MOVEit Transfer.

Чтобы настроить и проверить единый вход Azure AD в MOVEit Transfer, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя MOVEit Transfer](#creating-a-moveit-transfer-test-user)** требуется для создания в MOVEit Transfer пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении MOVEit Transfer.

**Чтобы настроить единый вход Azure AD в MOVEit Transfer, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **MOVEit Transfer** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в MOVEit Transfer?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
    
	.![Настройка единого входа](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)

3. На диалоговой странице **Configure App Settings** (Настройка параметров приложения) выполните следующие действия, а затем нажмите кнопку **Далее**.

    .![Настройка единого входа](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)

	а. В текстовое поле **URL-адрес для входа** введите URL-адрес входа с именем вашего домена.

    b. В текстовое поле **Идентификатор** введите URL-адрес идентификатора сущности.

    c. В текстовое поле **URL-адрес ответа** введите активированный URL-адрес интерфейса обработчика утверждений.

	г) Нажмите кнопку **Далее**.

	> [AZURE.NOTE] Обратите внимание, что вместо этих значений нужно указать фактический URL-адрес для входа и идентификатор. Чтобы узнать эти значения, выполните шаг 8 или свяжитесь с разработчиком [MOVEit Transfer](https://www.ipswitch.com/support/technical-support).

4. На странице **Настройка единого входа в MOVEit Transfer** выполните следующие действия и нажмите кнопку **Далее**.

	.![Настройка единого входа](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.

5. Войдите в клиент MOVEit Transfer с правами администратора.

6. В левой панели навигации щелкните **Settings** (Параметры).

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

7. Щелкните ссылку **Single Signon** (Единый вход), которая расположена в разделе **Security Policies -> User Auth** (Политики безопасности -> Проверка подлинности пользователя).

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

8. Щелкните ссылку с URL-адресом метаданных, чтобы скачать документ метаданных.

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)

	- Убедитесь, что значение **entityID** совпадает с **идентификатором** из шага 3.
	
	- Убедитесь, что URL-адрес расположения **AssertionConsumerService** совпадает с **URL-адресом ответа** из шага 3.

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

9. Щелкните кнопку **Add Identity Provider** (Добавить поставщик удостоверений), чтобы добавить новый федеративный поставщик удостоверений.

	![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

10. Щелкните **Browse** (Обзор), чтобы выбрать файл метаданных, который вы скачали на шаге 4, затем нажмите кнопку **Add Identity Provider** (Добавить поставщик удостоверений) и отправьте скачанный файл.

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

11. Выберите значение **Yes** (Да) для параметра **Enabled** (Включено) в разделе **Edit Federated Identity Provider Settings** (Изменение параметров федеративного поставщика удостоверений) и нажмите кнопку **Save** (Сохранить).

	 .![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

12. На странице **Edit Federated Identity Provider User Settings** (Изменение параметров федеративного поставщика удостоверений) выполните следующие действия и нажмите кнопку **Save** (Сохранить).

	а. Выберите значение **SAML NameID** для параметра **Login name** (Имя для входа).

	b. Выберите значение **Other** (Другое) для параметра **Full name** (Полное имя), а в текстовое поле **Attribute name** (Имя атрибута) введите такое значение: http://schemas.microsoft.com/identity/claims/displayname.

	c. Выберите значение **Other** (Другое) для параметра **Email** (Адрес электронной почты), а в текстовое поле **Attribute name** (Имя атрибута) введите такое значение: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress.

	г) Выберите значение **Yes** (Да) для параметра **Auto-create account on signon** (Автоматическое создание учетной записи при первом входе).

	д. Нажмите кнопку **Сохранить**.

	.![Настройка единого входа на стороне приложения](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)

13. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
	.![Единый вход в Azure AD][10]

14. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
	.![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

.![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя MOVEit Transfer

Цель этого раздела — создать пользователя с именем Britta Simon в MOVEit Transfer. Приложение MOVEit Transfer поддерживает JIT-подготовку, и вы уже включили ее.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению MOVEit Transfer (если он еще не создан).

> [AZURE.NOTE] Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки MOVEit Transfer.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к MOVEit Transfer.
	
.![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в MOVEit Transfer, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
    
	![Назначение пользователя][201]

2. В списке приложений выберите **MOVEit Transfer**.
    
	.![Настройка единого входа](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
    
	.![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
	.![Назначение пользователя][205]

### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку MOVEit Transfer на панели доступа, вы автоматически войдете в приложение MOVEit Transfer.

## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



.<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->
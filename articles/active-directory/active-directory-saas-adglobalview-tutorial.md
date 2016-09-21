<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с ADP GlobalView | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в ADP GlobalView."
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
	ms.date="09/07/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с ADP GlobalView

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением ADP GlobalView. Интеграция Azure AD с ADP GlobalView обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ADP GlobalView.
- Вы можете включить автоматический вход пользователей в ADP GlobalView (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с ADP GlobalView, вам потребуется:

- подписка Azure AD;
- подписка ADP GlobalView с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление ADP GlobalView из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление ADP GlobalView из коллекции
Чтобы настроить интеграцию ADP GlobalView с Azure AD, необходимо добавить ADP GlobalView из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ADP GlobalView из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **ADP GlobalView**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)

7. В области результатов выберите **ADP GlobalView** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в ADP GlobalView с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в ADP GlobalView соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ADP GlobalView. Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ADP GlobalView.

Чтобы настроить и проверить единый вход Azure AD в ADP GlobalView, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя ADP GlobalView](#creating-a-adp-globalview-test-user)** требуется для создания пользователя Britta Simon в ADP GlobalView, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение ADP GlobalView.

Приложение ADP GlobalView ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. Утверждение всегда будет носить имя **PersonImmutableID** и иметь значение, которое было сопоставлено с ExtensionAttribute2, содержащим EmployeeID пользователя. Здесь выполняется сопоставление пользователя из Azure AD с ADP GlobalView по значению EmployeeID, однако его можно сопоставить с другим значением, учитывая параметры приложения. Вместе со службой поддержки ADP GlobalView выполните действия по использованию правильного идентификатора пользователя и его сопоставлению с утверждением **PersonImmutableID**. Также можно сопоставить утверждения электронной почты и идентификатора пользователя, как показано на рисунке.
 
![Настройка единого входа](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png)

Чтобы настроить утверждение SAML, обратитесь в службу технической поддержки ADP GlobalView и запросите значение уникального идентификатора для вашего клиента. Это значение необходимо для настройки пользовательского утверждения для вашего приложения.


**Чтобы настроить единый вход Azure AD в ADP GlobalView, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **ADP GlobalView** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в ADP GlobalView?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 
	![Настройка единого входа](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png)


    а. В текстовом поле **Идентификатор** введите URL-адрес, используемый для идентификации приложения ADP GlobalView, в одном из следующих форматов: **https://<имя сервера>.globalview.adp.com/federate2**, **https://<имя сервера>.globalview.adp.com/federate**.


    b. В текстовом поле **URL-адрес ответа** введите URL-адрес, используемый Azure AD для отправки ответа приложению ADP GlobalView, в одном из следующих форматов: **https://<имя сервера>.globalview.adp.com/federate2/sp/ACS.saml2**, **https://<имя сервера>.globalview.adp.com/federate/sp/ACS.saml2**.

    c. Нажмите кнопку **Далее**.


4. На странице **Configure single sign-on at ADP GlobalView** (Настройка единого входа в ADP GlobalView) сделайте следующее:

	![Настройка единого входа](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Для настройки единого входа для своего приложения обратитесь в службу поддержки ADP GlobalView и предоставьте следующие сведения:

    - Скачанный файл сертификата
    - Идентификатор сущности
    - URL-адрес единого входа SAML
    - URL-адрес службы единого выхода


    > [AZURE.NOTE] После того как служба поддержки **ADP GlobalView** настроит экземпляр, получите у нее значение параметра **RelayState** и отправьте запрос в Azure AD на настройку значения для экземпляра приложения. Это значение, идентификатор клиента и идентификатор приложения Azure AD можно отправить в службу поддержки Azure AD по адресу [waadpartners@microsoft.com](mailTo:waadpartners@microsoft.com). Только выполнив этот этап настройки, вы сможете проверить работу интеграции. Обратите внимание, что этот этап настройки имеет важное значение для поддержки интеграции приложения.

6. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя ADP GlobalView

Цель этого раздела — создать пользователя с именем Britta Simon в приложении ADP GlobalView. Обратитесь в службу поддержки ADP GlobalView, чтобы добавить пользователей в учетную запись ADP GlobalView.


> [AZURE.NOTE] Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки ADP GlobalView.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к ADP GlobalView.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в ADP GlobalView, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. В списке приложений выберите **ADP GlobalView**.

	![Настройка единого входа](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув элемент ADP GlobalView на панели доступа, вы автоматически войдете в приложение ADP GlobalView.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->
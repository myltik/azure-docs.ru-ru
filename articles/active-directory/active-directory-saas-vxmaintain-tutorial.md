.<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с vxMaintain | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и vxMaintain."
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
	ms.date="08/04/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с vxMaintain

В этом учебнике описано, как интегрировать vxMaintain с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением vxMaintain обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к vxMaintain.
- Вы можете включить автоматический вход пользователей в vxMaintain (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с vxMaintain, вам потребуется:

- подписка Azure AD;
- подписка vxMaintain с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление vxMaintain из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление vxMaintain из коллекции
Чтобы настроить интеграцию vxMaintain с Azure AD, необходимо добавить vxMaintain из коллекции в список управляемых приложений SaaS.

**Чтобы добавить vxMaintain из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **vxMaintain**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_01.png)
7. В области результатов выберите **vxMaintain** и нажмите кнопку **Завершить**, чтобы добавить приложение.



##  Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в vxMaintain с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в vxMaintain соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в vxMaintain.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в vxMaintain.

Чтобы настроить и проверить единый вход Azure AD в vxMaintain, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя vxMaintain](#creating-a-vxmaintain-test-user)** требуется для создания пользователя Britta Simon в vxMaintain, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении vxMaintain.


**Чтобы настроить единый вход Azure AD в vxMaintain, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **vxMaintain** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
	 
	.![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в vxMaintain?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	.![Настройка единого входа](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_03.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_04.png)

    а. В текстовом поле **Identifier URL** (URL-адрес идентификатора) введите URL-адрес в следующем формате: **https://\<название\_компании>.verisae.com**.
	
	b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: **https://\<название\_компании>.verisae.com/DataNett/action/ssoConsume/mobile?\_log=true**.

	3\. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в vxMaintain** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для приложения, обратитесь к своему менеджеру по работе с клиентами в Verisae, который поможет вам настроить единый вход в вашей организации. Предоставьте ему следующие сведения:

	• скачанный файл **метаданных**.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
	
	.![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
 
	.![Единый вход в Azure AD][11]


### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


.![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя vxMaintain

В этом разделе описано, как создать пользователя Britta Simon в приложении vxMaintain. Обратитесь к своему менеджеру по работе с клиентами в Verisae, который поможет вам добавить пользователей в платформу vxMaintain.


### Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к vxMaintain.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в vxMaintain, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

	.![Назначение пользователя][201]

2. Из списка приложений выберите **vxMaintain**.

	.![Настройка единого входа](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]


### Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент vxMaintain на панели доступа, вы автоматически войдете в приложение vxMaintain.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->
<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с TargetProcess | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и TargetProcess."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с TargetProcess

Цель этого учебника — показать, как интегрировать TargetProcess с Azure Active Directory (Azure AD).

Интеграция TargetProcess с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к TargetProcess. 
- Вы можете включить автоматический вход пользователей в TargetProcess (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с приложением TargetProcess, вам потребуется следующее:

- подписка Azure AD;
- подписка TargetProcess с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление приложения TargetProcess из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление приложения TargetProcess из коллекции
Чтобы настроить интеграцию приложения TargetProcess с Azure AD, необходимо добавить TargetProcess из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TargetProcess из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **TargetProcess**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)

7. В области результатов выберите **TargetProcess** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении TargetProcess с использованием тестового пользователя Britta Simon.

Для включения единого входа в Azure AD необходимо знать, какой пользователь в приложении TargetProcess соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TargetProcess.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TargetProcess.
 
Чтобы настроить и проверить единый вход Azure AD в TargetProcess, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя TargetProcess](#creating-a-targetprocess-test-user)** требуется для создания в TargetProcess пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение TargetProcess. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Чтобы настроить единый вход в TargetProcess, вам понадобится зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки TargetProcess по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Чтобы настроить единый вход Azure AD в TargetProcess, выполните следующие действия.**

1. На классическом портале Azure AD на странице интеграции с приложением **TargetProcess** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в TargetProcess?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png)

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png)


    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение TargetProcess (например, **https://fabrikam.TargetProcess.com/*).

    b. Нажмите кнопку **Далее**.
 
 
4. На странице **Настройка единого входа в TargetProcess** выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png)

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. Войдите в приложение TargetProcess с правами администратора.


1. В верхнем меню щелкните **Настройка**.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

1. Щелкните **Параметры**.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png)

1. Щелкните **Single Sign-on** (Единый вход).

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png)

1. В диалоговом окне "Параметры единого входа" выполните следующие действия.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)

    а. Щелкните **Enable Single Sign-on** (Включить единый вход).

    b. На классическом портале Azure на странице **Настройка единого входа в TargetProcess** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес входа**.

    c. Откройте скачанный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Certificate** (Сертификат).

    d. Установите флажок **Enable JIT Provisioning** (Включить JIT-подготовку).


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon. В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png)
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png)
 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png)
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя TargetProcess

Цель этого раздела — создать в приложении TargetProcess пользователя с именем Britta Simon. Приложение TargetProcess поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

В этом разделе никакие действия с вашей стороны не требуются.




### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к TargetProcess.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению TargetProcess, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	![Назначение пользователя][201]

2. Из списка приложений выберите **TargetProcess**.

	![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	![Назначение пользователя][203]

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент TargetProcess на панели доступа, вы автоматически войдете в приложение TargetProcess.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->
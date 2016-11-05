---
title: Руководство. Интеграция Azure Active Directory с Flatter Files | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и Flatter Files.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с Flatter Files
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Flatter Files. Интеграция Azure AD с Flatter Files обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Flatter Files.
* Вы можете включить автоматический вход пользователей в Flatter Files (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Flatter Files, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа в Flatter Files.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Flatter Files из коллекции
2. Настройка и проверка единого входа в Azure AD.

## Добавление Flatter Files из коллекции
Чтобы настроить интеграцию Flatter Files с Azure AD, необходимо добавить Flatter Files из коллекции в список управляемых приложений SaaS.

**Как добавить Flatter Files из коллекции**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Flatter Files**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

1. В области результатов выберите **Flatter Files** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

## Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Flatter Files с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Flatter Files соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Flatter Files. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Flatter Files.

Чтобы настроить и проверить единый вход Azure AD в Flatter Files, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)**. Необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Flatter Files](#creating-a-halogen-software-test-user)**. Требуется для создания в Flatter Files пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение Flatter Files. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Для настройки единого входа в приложение Flatter Files вам потребуется зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Flatter Files по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com).

**Как настроить единый вход Azure AD в Flatter Files**

1. На классическом портале Azure на странице интеграции с приложением **Flatter Files** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в Flatter Files?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png)
3. На диалоговой странице **Настроить параметры приложения** нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png)
   
   > [!NOTE]
   > Приложение Flatter Files использует один и тот же URL-адрес единого входа для всех клиентов: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
   > 
   > 
4. На странице **Настройка единого входа в Flatter Files** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)
   
    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Войдите в приложение Flatter Files с правами администратора.
6. Щелкните элемент Панель мониторинга.
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)
7. Выберите элемент **Settings** (Параметры), а затем выполните следующие действия на вкладке **Company** (Компания).
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)
   
    а. Установите флажок **Use SAML 2.0 For Authentication** (Использовать SAML 2.0 для проверки подлинности).
   
    б) Нажмите кнопку **Configure SAML** (Настроить SAML).
8. В диалоговом окне **SAML Configuration** (Настройка SAML) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)
   
    а. В поле Domain (Домен) укажите свой зарегистрированный домен.
   
   > [!NOTE]
   > Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Flatter Files по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com).
   > 
   > 
   
    b. На классическом портале Azure в диалоговом окне "Настройка единого входа в Flatter Files" скопируйте URL-адрес службы единого входа и вставьте его в текстовое поле "URL-адрес поставщика удостоверений".
   
    c. Создайте файл в кодировке **Base-64** из загруженного сертификата.
   
   > [!TIP]
   > Подробности см. в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
   > 
   > 
   
    г) Откройте сертификат в кодировке Base-64 в «Блокноте», скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений FlatterFiles**.
   
    д) Нажмите кнопку **Обновить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
     ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png)
5. На диалоговой странице **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия:
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите кнопку **Завершить**.

### Создание тестового пользователя Flatter Files
Цель этого раздела — создать пользователя с именем Britta Simon в Flatter Files.

**Как создать пользователя с именем Britta Simon в Flatter Files**

1. Войдите на сайт своей компании в службе **Flatter Files** с правами администратора.
2. На панели навигации слева щелкните пункт **Settings** (Параметры), а затем выберите вкладку **Users** (Пользователи).
   
    ![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)
3. Нажмите кнопку **Add User** (Добавить пользователя).
4. На странице **Добавление пользователя** выполните следующие действия.
   
    ![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)
   
    а. В текстовом поле **Имя** введите **Britta**.
   
    б) В текстовое поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя Britta Simon на классическом портале Azure.
   
    г) Нажмите кнопку **Submit** (Отправить).

### Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Flatter Files.

![Назначение пользователя][200]

**Как назначить пользователя Britta Simon в Flatter Files**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Flatter Files**.
   
    ![Назначение пользователя](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкните элемент Flatter Files на панели доступа, чтобы автоматически войти в приложение Flatter Files.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->
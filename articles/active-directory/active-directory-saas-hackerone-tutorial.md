---
title: Руководство. Интеграция Azure Active Directory с HackerOne | Microsoft Docs
description: Узнайте, как настроить единый вход Azure Active Directory в HackerOne.
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
ms.date: 07/09/2016
ms.author: jeedes

---
# Учебник. Интеграция Azure Active Directory с HackerOn
В этом учебнике описано, как интегрировать HackerOn с Azure Active Directory (Azure AD).

Интеграция HackerOn с Azure AD дает приведенные далее преимущества:

* С помощью Azure AD вы можете контролировать доступ к HackerOn.
* Вы можете включить автоматический вход пользователей в HackerOn (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с HackerOn, вам потребуется:

* Подписка Azure
* подписка с поддержкой единого входа HackerOn.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление HackerOne из коллекции
2. Настройка и проверка единого входа в Azure AD

## Добавление HackerOne из коллекции
Чтобы настроить интеграцию HackerOn с Azure AD, необходимо добавить HackerOn из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HackerOn из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

![Приложения][4]

1. В поле поиска введите **HackerOne**.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

1. В области результатов выберите **HackerOne** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## Настройка и проверка единого входа в Azure AD
Далее выполняется настройка и проверка единого входа Azure AD в HackerOne с тестовым пользователем Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в HackerOn соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HackerOne. Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HackerOne.

Чтобы настроить и проверить единый вход Azure AD в HackerOne, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HackerOne](#creating-a-hackerone-test-user)** требуется для создания пользователя Britta Simon в HackerOne, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении HackerOne.

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

**Чтобы настроить единый вход Azure AD в HackerOne, выполните следующие действия:**

1. На странице интеграции с приложением **HackerOne** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в HackerOne?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png)
3. На диалоговой странице **Настроить параметры приложения** выполните действия, указанные ниже, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение HackerOne, в формате **https://hackerone.com/<имя\_компании>/authentication**.

    b. Обратитесь в службу поддержки HackerOne по адресу [support@hackerone.com](mailto:support@hackerone.com), чтобы получить URL-адрес клиента, если он вам неизвестен.

    c. В текстовом поле **Идентификатор** введите URL-адрес клиента.

    г) Нажмите кнопку **Далее**.


1. На странице **Настройка единого входа в HackerOne** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png)
   
    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Войдите в клиент HackerOne как администратор.
3. В верхнем меню щелкните **Settings** (Параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png)
4. Перейдите к разделу **Authentication** (Аутентификация) и щелкните **Add SAML settings** (Добавить параметры SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png)
5. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png)
   
    а. Введите имя зарегистрированного домена в поле **Email Domain** (Домен электронной почты).
   
    b. На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовое поле "Single Sign On URL" (URL-адрес единого входа).
   
    c. Создайте файл в кодировке **Base-64** из загруженного сертификата.
   
       >[AZURE.TIP] Подробности см. в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
   
    г) Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **X509 Certificate** (Сертификат X509).
   
    д. Щелкните **Save** (Сохранить).
6. В диалоговом окне "Authentication Settings" (Параметры аутентификации) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png)
   
    а. Щелкните **Run test** (Выполнить проверку).
   
    b. Если поле **Status** (Состояние) имеет значение **Last test status: created** (Состояние при последней проверке: создан), обратитесь в службу поддержки HackerOne по адресу [support@hackerone.com](mailto:support@hackerone.com), чтобы запросить проверку своей конфигурации.
7. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
Затем необходимо создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png)
5. На диалоговой странице **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На диалоговой странице **Получить временный пароль** щелкните **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png)
8. На диалоговой странице **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя HackerOne
Затем нужно создать пользователя Britta Simon в приложении HackerOne. Приложение HackerOne поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь, если он еще не существует, создается при доступе к HackerOne. [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки HackerOne.
> 
> 

### Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon применять единый вход Azure, предоставив ей доступ к HackerOne.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в HackerOne, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **HackerOne**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. Из списка пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
И, наконец, нужно проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкнув плитку HackerOne на панели доступа, вы автоматически войдете в приложение HackerOne.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->
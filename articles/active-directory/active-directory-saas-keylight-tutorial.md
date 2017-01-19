---
title: "Учебник. Интеграция Azure Active Directory с Keylight | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Keylight."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2e8346695d4f9e0b2d7016455d20e2f843047eed


---
# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Учебник. Интеграция Azure Active Directory с Keylight
В этом учебнике описано, как интегрировать Keylight с Azure Active Directory (Azure AD).

Интеграция Keylight с Azure AD дает приведенные далее преимущества.

* С помощью Azure AD вы можете контролировать доступ к Keylight.
* Вы можете включить автоматический вход пользователей в Keylight (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Keylight, вам потребуется:

* Подписка Azure
* подписка Keylight с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Keylight из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-keylight-from-the-gallery"></a>Добавление Keylight из коллекции
Чтобы настроить интеграцию Keylight с Azure AD, необходимо добавить Keylight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Keylight из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Keylight**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)
7. В области результатов выберите **Keylight** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Keylight с использованием тестового пользователя Britta Simon.

Чтобы настроить и проверить единый вход Azure AD в Keylight, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Keylight](#creating-a-keylight-test-user)** требуется для создания пользователя Britta Simon в Keylight, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале Azure и настроить его в приложении Keylight.

**Чтобы настроить единый вход Azure AD в Keylight, выполните следующие действия:**

1. На странице интеграции с приложением **Keylight** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Keylight?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 

    а. В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый для входа в приложение Keylight, в следующем формате: **https://\<название_компании\>.keylightgrc.com/Login.aspx?saml=1**.


1. На странице **Настройка единого входа в Keylight** сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы включить единый вход в Keylight, выполните следующие действия:
   
    а. Войдите в учетную запись Keylight от имени администратора.
   
    b. В меню вверху щелкните **Person** (Пользователь) и выберите **Keylight Setup** (Настройка Keylight).
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/401.png) 
   
    c. В представлении в виде дерева слева щелкните **SAML**.
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/402.png) 
   
    d. В диалоговом окне **SAML Settings** (Параметры SAML) нажмите кнопку **Edit** (Изменить).
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/404.png) 
3. На странице диалогового окна **Изменение параметров SAML** выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    а. Задайте для параметра **SAML authentication** (Аутентификация SAML) значение **Active** (Активно).

    b. На классическом портале Azure AD скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений).

    В. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений).

    d. Щелкните **Choose File** (Выбрать файл), чтобы выбрать скачанный сертификат Keylight, а затем нажмите кнопку **Open** (Открыть), чтобы передать сертификат.


    д. В поле **SAML User Id location** (Расположение идентификатора пользователя SAML) выберите значение **NameIdentifier element of the subject statement** (Элемент NameIdentifier оператора Subject).

    Е. Введите значение **Keylight Service Provider (Поставщик услуг Keylight), используя следующий формат: **https://&lt;название_компании&gt;.keylightgrc.com**.

    g. Задайте для параметра **Auto-provision users** (Автоматическая подготовка пользователей) значение **Active** (Активно).

    h. Задайте для параметра **Auto-provision account type** (Тип учетной записи для автоматической подготовки) значение **Full User** (С полным доступом).

    i. Задайте для параметра **Auto-provision security role** (Роль безопасности для автоматической подготовки) значение **Standard User with SAML** (Права обычного пользователя с SAML).

    j. Задайте для параметра **Auto-provision security config** (Конфигурация безопасности для автоматической подготовки) значение **Standard User Configuration** (Конфигурация обычного пользователя).

    k. В текстовом поле "Email attribute" (Атрибут адреса электронной почты) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. В текстовом поле **First name attribute** (Атрибут имени) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. В текстовом поле **Last name attribute** (Атрибут фамилии) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Щелкните **Сохранить**.




1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале Azure тестового пользователя с именем Britta Simon.

В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-keylight-test-user"></a>Создание тестового пользователя Keylight
В этом разделе описано, как создать пользователя Britta Simon в приложении Keylight. Приложение Keylight поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь, если он еще не существует, создается при доступе к Keylight. 

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Keylight.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Keylight.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Keylight, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Keylight**.
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Keylight на панели доступа, вы автоматически войдете в приложение Keylight.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->



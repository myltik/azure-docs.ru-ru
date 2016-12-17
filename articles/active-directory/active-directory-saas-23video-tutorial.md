---
title: "Руководство по интеграции Azure Active Directory с платформой 23 Video | Документация Майкрософт"
description: "Узнайте, как настроить единый вход для Azure Active Directory и платформы 23 Video."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 605ba0aee99b81c07e37e13314e7bc1226e7e161


---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Руководство по интеграции Azure Active Directory с платформой 23 Video
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением 23 Video.  
Интеграция Azure AD с приложением 23 Video обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете управлять доступом к 23 Video. 
* Вы можете включить автоматический вход пользователей в 23 Video (единый вход) с учетной записью Azure AD.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с 23 Video, вам потребуется:

* подписка Azure AD;
* подписка на 23 Video с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление 23 Video из коллекции. 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-23-video-from-the-gallery"></a>Добавление 23 Video из коллекции.
Чтобы настроить интеграцию 23 Video с Azure AD, необходимо добавить 23 Video из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 23 Video из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **23 Video**.
   
    ![Приложения][5]
7. В области результатов выберите **23 Video** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][25]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в 23 Video с использованием тестового пользователя Britta Simon.

Для настройки единого входа в Azure AD необходимо знать, какой пользователь в 23 Video соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 23 Video.  
Чтобы установить эту связь, добавьте **имя пользователя** Azure AD в качестве значения **имени пользователя** в 23 Video.

Чтобы настроить и проверить единый вход Azure AD в 23 Video, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя 23 Video](#creating-a-23-video-test-user)** требуется для создания в 23 Video пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении 23 Video.

**Чтобы настроить единый вход Azure AD в 23 Video, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **23 Video** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в 23 Video** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Единый вход в Azure AD][8] 
   
     а. В текстовом поле **URL-адрес ответа** введите URL-адрес, используемый для входа на сайт 23 Video (например, *https://britta-simon.23Video.com/saml/login*).
   
   > [!NOTE]
   > Интеграция Active Directory с помощью SAML 2.0 доступна для всех пользователей 23 Video. Если вам нужны соответствующие метаданные, обратитесь в службу поддержки по адресу [support@23company.com](mailto:support@23company.com) .
   > 
   > 
   
     b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в 23 Video** выполните следующие действия.
   
    ![Единый вход в Azure AD][9] 
   
    а. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.
   
    b. Обратитесь в службу поддержки 23 Video по адресу [support@23company.com](mailto:support@23company.com), предъявите скачанный сертификат, сообщите **URL-адрес издателя**, **URL-адрес единого входа**, **URL-адрес единого выхода**, а затем попросите настроить единый вход для вашего приложения 23 Video. 
   
    c. Нажмите кнопку **Далее**.
5. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]
6. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-23-video-test-user"></a>Создание тестового пользователя 23 Video
Цель этого раздела — создать в 23 Video пользователя с именем Britta Simon.

**Чтобы создать в 23 Video пользователя с именем Britta Simon, выполните следующие действия:**

1. Войдите на сайт компании 23 Video от имени администратора.
2. Перейдите в меню **Параметры**.
3. В разделе **Users** (Пользователи) щелкните **Configure** (Настройка).
   
    ![Назначение пользователя][400]
4. Щелкните **Add a new user**(Добавить нового пользователя). 
   
    ![Назначение пользователя][401]
5. В разделе **Invite someone to join this site** (Пригласить присоединиться к сайту) выполните следующие действия.
   
    ![Назначение пользователя][402]
   
    а. В текстовом поле **E-mail addresses** (Адреса электронной почты) введите адрес электронной почты пользователя Britta Simon в Azure AD.
   
    b. Щелкните **Add the user**(Добавить пользователя).   

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к 23 Video.

![Назначение пользователя][200] 

**Чтобы добавить пользователя Britta Simon в 23 Video, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **23 Video**.
   
    ![Назначение пользователя][202] 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку 23 Video на панели доступа, вы автоматически войдете в приложение 23 Video.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png







<!--HONumber=Nov16_HO3-->



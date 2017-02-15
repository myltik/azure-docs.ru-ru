---
title: "Учебник. Интеграция Azure Active Directory с MCM | Документация Майкрософт"
description: "Узнайте, как использовать MCM с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f00799d-e3e9-4ba9-ae4a-fbca843ac5db
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51a736c051bef8550662060fdde89fc50e32928a


---
# <a name="tutorial-azure-active-directory-integration-with-mcm"></a>Руководство. Интеграция Azure Active Directory с МСМ
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением MCM.

Интеграция Azure AD с приложением MCM обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к MCM.
* Вы можете включить автоматический вход пользователей в MCM (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с MCM, вам потребуется:

* Действующая подписка на Azure
* подписка на MCM с поддержкой единого входа.

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

1. Добавление MCM из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-mcm-from-the-gallery"></a>Добавление MCM из коллекции
Чтобы настроить интеграцию MCM с Azure AD, необходимо добавить MCM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MCM из коллекции, сделайте следующее:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Add an application from gallerry")

6. В **поле поиска** введите **MCM**.
   
    ![Коллекция приложений](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Application gallery")

7. В области результатов выберите **MCM** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в MCM с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в MCM соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MCM.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в MCM.

Чтобы настроить и проверить единый вход Azure AD в MCM, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя MCM](#creating-a-mcm-test-user)** требуется для создания в MCM пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении MCM.

**Чтобы настроить единый вход Azure AD в MCM, сделайте следующее:**

1. На странице интеграции с приложением **MCM** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configure single sign-on")

2. На странице **Как пользователи должны входить в MCM?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Единый вход Microsoft Azure AD](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3. В диалоговом окне на странице "Настройка параметров приложения" выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configure App URL")
   
    а. В текстовом поле **URL-адрес для входа** введите `https://myaba.co.uk/client-access/<company name>/saml.php`.
   
    b. click **Далее**

4. На странице **Настройка единого входа в MCM** нажмите кнопку **Скачать метаданные**, а затем сохраните файл сертификата на своем компьютер.
   
    ![Настройка единого входа](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configure Single Sign-On")

5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки MCM. Присоедините к сообщению скачанный файл метаданных, чтобы специалисты MCM смогли настроить единый вход со своей стороны.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configure Single Sign-On")

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Настройка единого входа](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-mcm-test-user"></a>Создание тестового пользователя MCM
В этом разделе описано, как создать пользователя Britta Simon в приложении MCM. Обратитесь в службу поддержки MCM, чтобы добавить пользователей на платформу MCM.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя MCM или API, предоставляемые MCM для подготовки учетных записей пользователя AAD.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к MCM.

![Назначение пользователей](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assign users")

**Чтобы назначить пользователя Britta Simon в MCM, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователей](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assign users")

2. В списке приложений выберите **MCM**.
   
    ![Настройка единого входа](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователей](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assign users")

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователей](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку MCM на панели доступа, вы автоматически войдете в приложение MCM.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->



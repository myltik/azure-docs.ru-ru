---
title: "Руководство по интеграции Azure Active Directory с @Task| Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d729d0859a3cdd0e676507c4fa772b7b8d3ed181


---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Учебник. Интеграция Azure Active Directory с @Task
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением @Task.  
Интеграция Azure AD с приложением @Task обеспечивает следующие преимущества: 

* С помощью Azure AD вы можете контролировать доступ к @Task.
* Вы можете включить автоматический вход пользователей в @Task (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — на классическом портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с @Task,, вам потребуется:

* подписка Azure AD;
* подписка на @Task с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление @Task из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-task-from-the-gallery"></a>Добавление @Task из коллекции
Чтобы настроить интеграцию @Task с Azure AD, необходимо добавить @Task из коллекции в список управляемых приложений SaaS.

**Чтобы добавить @Task из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1] 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2] 
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3] 
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4] 
6. В поле поиска введите **@Task**.
   
    ![Приложения][5] 
7. В области результатов выберите **@Task** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в @Task с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в @Task соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в @Task.   
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в @Task..

Чтобы настроить и проверить единый вход Azure AD в @Task,, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя @Tasktest](#creating-a-halogen-software-test-user)** требуется для создания в @Taskthat пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение @Task.

**Чтобы настроить единый вход Azure AD в @Task,, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **@Task** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в @Task?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка параметров приложения][8] 
   
     а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение @Task (например, *https://<Tenant name>.attask-ondemand.com*).
   
     b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в @Task** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере и щелкните **Далее**.
   
    ![Что такое Azure AD Connect?][9] 
5. Выполните вход на сайт компании @Task в качестве администратора.
6. Перейдите к **Настройке единого входа**.
7. В диалоговом окне **Единый вход** выполните следующие действия.
   
    ![Настройка единого входа][23]
   
    а. Для параметра **Тип** выберите значение **SAML 2.0**.
   
    b. Выберите **идентификатор поставщика службы**.
   
    c. На классическом портале Azure скопируйте **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login Portal URL** (URL-адрес портала входа).
   
    d. На классическом портале Azure скопируйте **URL-адрес службы единого выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
   
    д. На классическом портале Azure скопируйте адрес в поле **Изменить URL-адрес пароля** и вставьте его в текстовое поле **Изменить URL-адрес пароля**.
   
    Е. Щелкните **Сохранить**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Что такое Azure AD Connect?][10]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Что такое Azure AD Connect?][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-task-test-user"></a>Создание тестового пользователя @Task
Цель этого раздела — создать пользователя с именем Britta Simon в @Task.

**Чтобы создать пользователя с именем Britta Simon в @Task,, выполните следующие действия.**

1. Выполните вход на сайт компании @Task в качестве администратора.
2. В верхнем меню щелкните **Пользователи**.
3. Щелкните **Новый пользователь**. 
4. В диалоговом окне "Новый пользователь" выполните следующие действия:
   
    ![Создание тестового пользователя @Task][21] 
   
    а. В текстовом поле **Имя** введите Britta.
   
    b. В текстовом поле **Фамилия** введите Simon.
   
    c. В текстовом поле **адрес электронной почты** введите адрес электронной почты пользователя Britta Simon в Azure Active Directory.
   
    d. Нажмите кнопку **Добавить пользователя**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к @Task.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении @Task,, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **@Task**.
   
    ![Назначение пользователя][202] 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент @Task на панели доступа, вы автоматически войдете в приложение @Task.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png









<!--HONumber=Nov16_HO3-->



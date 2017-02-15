---
title: "Руководство по интеграции Azure Active Directory с порталом управления облачными службами для Microsoft Azure | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и порталом управления облачными службами для Microsoft Azure."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0c89d0839402130bc2badc5f33cd633a1e5b7da


---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Учебник. Интеграция Azure Active Directory с порталом управления облачными службами для Microsoft Azure
Цель этого руководства — показать, как интегрировать портал управления облачными службами для Microsoft Azure с Azure Active Directory (Azure AD).  
Интеграция портала управления облачными службами для Microsoft Azure с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ пользователей к порталу управления облачными службами для Microsoft Azure.
* Вы можете включить автоматический вход пользователей в портал управления облачными службами (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с порталом управления облачными службами для Microsoft Azure, вам потребуется следующее:

* подписка Azure AD;
* подписка портала управления облачными службами для Microsoft Azure с поддержкой единого входа.

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

1. Добавление портала управления облачными службами для Microsoft Azure из коллекции
2. Настройка и проверка единого входа в Azure AD.

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Добавление портала управления облачными службами для Microsoft Azure из коллекции
Чтобы настроить интеграцию с портала управления облачными службами для Microsoft Azure с Azure AD, необходимо добавить портал управления облачными службами для Microsoft Azure из коллекции в список управляемых приложений SaaS.

**Чтобы добавить портал управления облачными службами для Microsoft Azure из коллекции, выполните приведенные ниже действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Портал управления облачными службами для Microsoft Azure**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_01.png)
7. В области результатов выберите **Портал управления облачными службами для Microsoft Azure** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD на портал управления облачными службами для Microsoft Azure с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь на портале управления облачными службами для Microsoft Azure соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем на портале управления облачными службами для Microsoft Azure.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** на портале управления облачными службами для Microsoft Azure.

Чтобы настроить и проверить единый вход Azure AD на портал управления облачными службами для Microsoft Azure, вам потребуется выполнить приведенные далее действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя портала управления облачными службами для Microsoft Azure](#creating-a-newsignature-test-user)** требуется для создания пользователя Britta Simon на портале управления облачными службами для Microsoft Azure, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение портала управления облачными службами для Microsoft Azure.

**Чтобы настроить единый вход Azure AD на портал управления облачными службами для Microsoft Azure, вам потребуется выполнить приведенные далее действия.**

1. На странице интеграции с **приложением портала управления облачными службами для Microsoft Azure** классического портала Azure AD нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить на портал управления облачными службами для Microsoft Azure** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_03.png) 
3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_04.png) 
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение портала управления облачными службами для Microsoft Azure, в следующем формате: `https://portal.igcm.com/<instance name>`.
   
    b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа на портал управления облачными службами для Microsoft Azure** выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Чтобы получить данные единого входа, настроенные для вашего приложения, обратитесь в службу поддержки портала управления облачными службами для Microsoft Azure по адресу [jczernuszka@newsignature.com](mailTo:jczernuszka@newsignature.com) , прикрепив загруженный файл сертификата. Также укажите URL-адрес издателя, URL-адрес единого входа SAML и URL-адрес службы выхода, чтобы настроить их для интеграции единого входа.
6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Создание тестового пользователя портала управления облачными службами для Microsoft Azure
Цель этого раздела — создать пользователя с именем Britta Simon на портале управления облачными службами для Microsoft Azure. Обратитесь в службу поддержки портала управления облачными службами для Microsoft Azure, чтобы добавить пользователей в учетную запись портала. 

> [!NOTE]
> Если вам требуется вручную создать пользователя, необходимо обратиться в службу поддержки портала управления облачными службами для Microsoft Azure.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления ей доступа на портал управления облачными службами для Microsoft Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon порталу управления облачными службами для Microsoft Azure, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **портал управления облачными службами для Microsoft Azure**.
   
    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент портала управления облачными службами для Microsoft Azure на панели доступа, вы автоматически войдете в приложение портала управления облачными службами для Microsoft Azure.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->



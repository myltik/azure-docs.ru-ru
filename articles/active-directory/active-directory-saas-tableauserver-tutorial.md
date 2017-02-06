---
title: "Руководство по интеграции Azure Active Directory с Tableau Server | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Tableau Server."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 344309eb9fc1ee7619df4cb6f692dd1697c4fb56


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Учебник. Интеграция Azure Active Directory с Tableau Server
Цель этого учебника — показать, как интегрировать Tableau Server с Azure Active Directory (Azure AD).

Интеграция Tableau Server с Azure AD дает приведенные далее преимущества:

* С помощью Azure AD вы можете контролировать доступ к Tableau Server.
* Вы можете включить автоматический вход пользователей в Tableau Server (единый вход) с применением учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением Tableau Server, вам потребуется следующее:

* подписка Azure AD;
* подписка Tableau Server с поддержкой единого входа.

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

1. Добавление сервера Tableau Server из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Добавление сервера Tableau Server из коллекции
Чтобы настроить интеграцию Tableau Server с Azure AD, необходимо добавить Tableau Server из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Tableau Server из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Tableau Server**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)
7. В области результатов выберите **Tableau Server** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении Tableau Server с использованием тестового пользователя Britta Simon.

Для включения единого входа в Azure AD необходимо знать, какой пользователь в приложении Tableau Server соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Tableau Server.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Tableau Server.

Чтобы настроить и проверить единый вход Azure AD в Tableau Server, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Tableau Server](#creating-a-tableauserver-test-user)** требуется для создания в Tableau Server пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Tableau Server.

Приложение Tableau Server ожидает утверждения SAML в определенном формате. На следующем снимке экрана приведен пример. 

![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Чтобы настроить единый вход Azure AD в Tableau Server, выполните следующие действия:**

1. На странице интеграции с приложением **Tableau Server** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 
2. В диалоговом окне **Атрибуты токена SAML** сделайте следующее.

    а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 


    b. В текстовом поле **Имя атрибута** введите значение **имени пользователя**.

    В. В списке **Значение атрибута** выберите **user.displayname**.

    d. Нажмите **Завершено**.    




1. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  
2. Щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
3. На странице **Как пользователи должны входить в Tableau Server** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 
4. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 

    а. В текстовое поле **URL-адрес входа** введите URL-адрес Tableau Server. 

    b. В поле "Идентификатор" скопируйте 

    c. Щелкните **Далее**


1. На странице **Настройка единого входа в Tableau Server** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


1. Чтобы единый вход был настроен для вашего приложения, войдите в клиент Tableau Server с правами администратора.
   
    а. В окне конфигурации Tableau Server откройте вкладку **SAML** .
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 

    b. Установите флажок **Использовать SAML для единого входа**.

    c. Найдите файл метаданных федерации, скачанный с классического портала Azure, а затем передайте его в **файл метаданных поставщика удостоверений SAML**.

    d. URL-адрес возврата Tableau Server — URL-адрес для доступа пользователей Tableau Server, например http://tableau_server. Мы не рекомендуем использовать http://localhost. URL-адреса с конечной косой чертой (например http://tableau_server/) не поддерживаются. Скопируйте **URL-адрес возврата Tableau Server** и вставьте его в текстовое поле **URL-адрес входа Azure AD**, как показано в шаге 3.

    д. Идентификатор сущности SAML — идентификатор сущности однозначно определяет установку Tableau Server для поставщика удостоверений. Если нужно, здесь можно еще раз ввести URL-адрес Tableau Server, но это необязательно должен быть URL-адрес Tableau Server. Скопируйте **идентификатор сущности SAML** и вставьте его в текстовое поле **идентификатор** Azure AD, как показано в шаге 3.

    Е. Щелкните **Экспорт файла метаданных** и откройте его в текстовом редакторе. Найдите URL-адрес службы обработчика утверждений с Http Post и индексом 0 и скопируйте URL-адрес. Вставьте его в текстовое поле **URL-адрес ответа** Azure AD, как показано в шаге 3. 

    ж. Нажмите кнопку **ОК** на странице конфигурации Tableau Server.

    > [AZURE.NOTE] Более подробные сведения о настройке SAML в Tableau Server см. в статье о [настройке SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm). 

1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. 
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 
   
    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-tableau-server-test-user"></a>Создание тестового пользователя Tableau Server
Цель этого раздела — создать в приложении Tableau Server пользователя с именем Britta Simon. Необходимо подготовить всех пользователей в приложении Tableau Server. Обратите внимание, что имя пользователя должно совпадать со значением, настроенным в пользовательском атрибуте Azure AD **username**. В случае правильного сопоставления интеграция должна обеспечить [настройку единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Если необходимо создать пользователя вручную, обратитесь к администратору Tableau Server в вашей организации.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Tableau Server.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Tableau Server, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Tableau Server**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.

![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Tableau Server на панели доступа, вы автоматически войдете в приложение Tableau Server.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->



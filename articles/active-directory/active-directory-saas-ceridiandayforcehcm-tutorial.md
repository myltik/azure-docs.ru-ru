---
title: "Руководство. Интеграция Azure Active Directory с Ceridian Dayforce HCM | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Ceridian Dayforce HCM."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e0056af13bfa064d740205746a93afeef9b33ab


---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Учебник. Интеграция Azure Active Directory с Ceridian Dayforce HCM
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Ceridian Dayforce HCM.  
Интеграция Azure AD с приложением Ceridian Dayforce HCM обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Ceridian Dayforce HCM.
* Вы можете включить автоматический вход пользователей в Ceridian Dayforce HCM (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Ceridian Dayforce HCM, вам потребуется:

* подписка Azure AD;
* подписка Ceridian Dayforce HCM с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Ceridian Dayforce HCM из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Добавление Ceridian Dayforce HCM из коллекции
Чтобы настроить интеграцию Ceridian Dayforce HCM с Azure AD, необходимо добавить Ceridian Dayforce HCM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ceridian Dayforce HCM из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Ceridian Dayforce HCM**.
   
    ![Приложения](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)
7. В области результатов выберите **Ceridian Dayforce HCM** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Ceridian Dayforce HCM с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Ceridian Dayforce HCM соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ceridian Dayforce HCM.

Чтобы настроить и проверить единый вход Azure AD в Ceridian Dayforce HCM, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Ceridian Dayforce HCM](#creating-a-ceridian-dayforce-hcm-test-user)** требуется для создания пользователя Britta Simon в Ceridian Dayforce HCM, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении Ceridian Dayforce HCM.

Приложение Ceridian Dayforce HCM ожидает утверждения SAML в определенном формате. Сначала обратитесь в службу поддержки Dayforce HCM, чтобы определить правильный идентификатор пользователя. Корпорация Майкрософт рекомендует использовать атрибут **"name"** в качестве идентификатора пользователя. Управлять значением этого атрибута можно в диалоговом окне **Атрибут** . На следующем снимке экрана приведен пример. 

![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**Чтобы настроить единый вход Azure AD в Ceridian Dayforce HCM, выполните следующие действия:**

1. На странице интеграции с приложением **Ceridian Dayforce HCM** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 
2. В списке **Атрибуты токена SAML** выберите имя атрибута и нажмите кнопку **Изменить**.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 
3. В диалоговом окне **Изменить атрибут пользователя** выполните следующие действия.
   
    а. В списке **Значение атрибута** выберите атрибут пользователя, который хотите использовать в своей реализации.  
    Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите **user.extensionattribute2**. 
   
    b. Нажмите **Завершено**.    
4. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  
5. На классическом портале Azure на странице интеграции с приложением **Ceridian Dayforce HCM** щелкните **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
6. На странице **Как пользователи должны входить в Ceridian Dayforce HCM?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 
7. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Ceridian Dayforce HCM. Для рабочих сред используйте следующий формат URL-адреса: `https://sso.dayforcehcm.com/DayforcehcmNamespace`

    Для ясности замените DayforcehcmNamespace пространством имен своей среды или идентификатором компании, например: `https://sso.dayforcehcm.com/contoso`

    Для тестовых сред используйте следующий формат URL-адреса: `https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес, используемый Azure AD для размещения ответа.  
    Для рабочих сред используйте `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    Для сред тестирования используйте `https://fs-test.dayforcehcm.com/sp/ACS.saml2`  


1. На странице **Настройка единого входа в Ceridian Dayforce HCM** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Ceridian Dayforce HCM по электронной почте и предоставьте следующие сведения:
   
   * Скачанный файл сертификата
   *  **URL-адрес издателя**
   *  **URL-адрес единого входа SAML** 
   *  **URL-адрес службы единого выхода** 
3. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>Создание тестового пользователя Ceridian Dayforce HCM
Цель этого раздела — создать пользователя с именем Britta Simon в Ceridian Dayforce HCM. 

Обратитесь в службу поддержки Ceridian Dayforce HCM, чтобы добавить пользователей в свое приложение Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Ceridian Dayforce HCM.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Ceridian Dayforce HCM, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Ceridian Dayforce HCM**.
   
    ![Настройка единого входа](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент Ceridian Dayforce HCM на панели доступа, вы автоматически войдете в приложение Ceridian Dayforce HCM.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->



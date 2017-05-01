---
title: "Руководство по интеграции Azure Active Directory с приложением &quot;Люди&quot; | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в приложение &quot;Люди&quot;."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f0b7010b75c612cb1112d7414bab1617844dfa65
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>Руководство. Интеграция Azure Active Directory с приложением "Люди"
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением "Люди".

Интеграция Azure AD с приложением "Люди" обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к приложению "Люди".
* Вы можете включить автоматический вход пользователей в People (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно с помощью классического портала Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением "Люди", вам потребуется:

* Подписка Azure
* подписка на People с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения "Люди" из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-people-from-the-gallery"></a>Добавление приложения People из коллекции
Чтобы настроить интеграцию приложения "Люди" с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение "Люди" из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Люди**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)
7. В области результатов выберите **Люди** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложение People с использованием тестового пользователя Britta Simon.

Чтобы настроить и проверить единый вход Azure AD в приложение "Люди", вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения "Люди"](#creating-a-people-test-user)** требуется для создания пользователя Britta Simon в приложении "Люди", связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — показать, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении People.

**Чтобы настроить единый вход Azure AD в People, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Люди** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    [Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в приложение "Люди"?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_03.png) 
3. На странице диалогового окна **Настроить параметры приложения** выполните описанные ниже действия, после чего нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_04.png) 
   
   1. В текстовом поле **URL-адрес входа** введите URL-адрес, который используется пользователями для входа в приложение "Люди", по следующему шаблону: **https://\<имя_компании\>.peoplehr.com/**. 
   2. Если URL-адрес клиента неизвестен, обратитесь в службу поддержки приложения "Люди" по адресу [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) , чтобы получить его.    3. В текстовом поле **Идентификатор** введите URL-адрес клиента. 
   4. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: **https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**.
   5. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в приложение "Люди"** выполните действия, указанные ниже, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_05.png) 
   
   1. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   2. Нажмите кнопку **Далее**.
5. Чтобы настроить для приложения единый вход, нужно войти в клиент приложения "Люди" с правами администратора.
   
   1. В меню слева выберите **Параметры**.
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)    
   2. Щелкните **Компания**.
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_002.png) 
    3. Чтобы отправить скачанный файл метаданных, в разделе **Upload Single Sign On SAML meta-data file** (Отправить файл метаданных SAML для единого входа) нажмите кнопку **Browse** (Обзор).
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)
6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

 * В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя приложения "Люди" в Azure AD, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_05.png) 
   
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-people-tutorial/create_aaduser_08.png) 
   
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="create-a-people-test-user"></a>Создание тестового пользователя в People
Цель этого раздела — создать пользователя с именем Britta Simon в приложении "Люди". В приложении "Люди" не поддерживается JIT-подготовка, поэтому нужно обратиться в службу поддержки этого приложения, чтобы создать пользователя вручную.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к People.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении "Люди", сделайте следующее:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Люди**.
   
    ![Настройка единого входа](./media/active-directory-saas-people-tutorial/tutorial_people_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Люди" на панели доступа, вы автоматически войдете в приложение "Люди".

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png


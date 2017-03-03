---
title: "Учебник. Интеграция Azure Active Directory с KnowBe4 | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в KnowBe4."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: bc7ce4867180dd9f896b894b56a02bf033ed6bbb
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-knowbe4"></a>Руководство. Интеграция Azure Active Directory с KnowBe4
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением KnowBe4.  

Интеграция Azure AD с приложением KnowBe4 обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к KnowBe4.
* Вы можете включить автоматический вход пользователей в KnowBe4 (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с KnowBe4, вам потребуется:

* подписка Azure AD;
* подписка KnowBe4 с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

* Добавление KnowBe4 из коллекции
* Настройка и проверка единого входа в Azure AD

## <a name="add-knowbe4-from-the-gallery"></a>Добавление KnowBe4 из коллекции
Чтобы настроить интеграцию KnowBe4 с Azure AD, необходимо добавить KnowBe4 из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowBe4 из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **KnowBe4**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_01.png)
7. В области результатов выберите **KnowBe4** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в KnowBe4 с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в KnowBe4 соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowBe4.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в KnowBe4.

Чтобы настроить и проверить единый вход Azure AD в KnowBe4, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя KnowBe4](#creating-a-KnowBe4-test-user)** требуется для создания пользователя Britta Simon в KnowBe4, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение KnowBe4.

**Чтобы настроить единый вход Azure AD в KnowBe4, сделайте следующее:**

1. На странице интеграции с приложением **KnowBe4** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в KnowBe4?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_04.png) 

  * В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый для входа в приложение KnowBe4, в следующем формате: **https://\<имя_компании\>.knowbe4.com/auth/saml/aad168.ccsctp.net)**.

4. На странице **Настройка единого входа в KnowBe4** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_05.png) 
   
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер. 
   2. Нажмите кнопку **Далее**.
5. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки KnowBe4](mailto:support@knowbe4.com). Вложите в сообщение скачанный файл сертификата и укажите URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода), чтобы специалисты KnowBe4 смогли настроить единый вход со своей стороны.
6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_05.png) 
   
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».   
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_08.png) 
   
    1. Запишите значение поля **Новый пароль**.
    2. Нажмите **Завершено**.   

### <a name="create-a-knowbe4-test-user"></a>Создание тестового пользователя KnowBe4
В этом разделе описано, как создать пользователя с именем Britta Simon в приложении KnowBe4. Приложение KnowBe4 поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к KnowBe4 будет создан пользователь (если он еще не создан). 

>[!NOTE]
>Если вам нужно создать пользователя вручную, обратитесь в службу поддержки KnowBe4.
>  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к KnowBe4.

    ![Assign User][200] 

**Чтобы назначить пользователя Britta Simon в KnowBe4, сделайте следующее:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **KnowBe4**.
   
    ![Настройка единого входа](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент KnowBe4 на панели доступа, вы автоматически войдете в приложение KnowBe4.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_205.png


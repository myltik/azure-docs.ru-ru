---
title: "Руководство по интеграции Azure Active Directory с 10,000ft Plans | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в 10,000ft Plans."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2a1f3df856116e372518b2f88460b3c6905c4c9a
ms.openlocfilehash: 584ddcfc53afee6a8ff11a03d662d8acf58e4820
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Учебник. Интеграция Azure Active Directory с 10,000ft Plans
Цель этого учебника — показать, как интегрировать приложение 10,000ft Plans с Azure Active Directory (Azure AD).  
Интеграция 10,000ft Plans с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к 10,000ft Plans.
* Вы можете включить автоматический вход пользователей в 10,000ft Plans (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с 10,000ft Plans, вам потребуется:

* подписка Azure AD;
* подписка 10,000ft Plans с поддержкой единого входа.

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

1. Добавление 10,000ft Plans из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-10000ft-plans-from-the-gallery"></a>Добавление 10,000ft Plans из коллекции
Чтобы настроить интеграцию 10,000ft Plans с Azure AD, необходимо добавить 10,000ft Plans из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 10,000ft Plans из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **10,000ft Plans**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_01.png)
7. В области результатов выберите **10,000ft Plans** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в 10,000ft Plans с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в 10,000ft Plans соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 10,000ft Plans.  
Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в 10,000ft Plans.

Чтобы настроить и проверить единый вход Azure AD в 10,000ft Plans, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя 10,000ft Plans](#creating-a-10000ft-plans-test-user)** требуется для создания пользователя Britta Simon в Certify, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение 10,000ft Plans.

**Чтобы настроить единый вход Azure AD в 10,000ft Plans, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **10,000ft Plans** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в 10,000ft Plans?** выберите **Единый вход Azure AD** и щелкните **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_03.png) 
3. На странице диалогового окна **Настроить параметры приложения** выполните описанные ниже действия, после чего нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_04.png) 

    а. В текстовом поле **URL-адрес входа** введите `https://app.10000ft.com`.

    b. В текстовом поле **Идентификатор** введите `https://app.10000ft.com/saml/metadata`.

    > [!NOTE] 
    > Значение для **идентификатора** отличается, если у вас есть личный домен. Если вам требуется помощь, обратитесь в [службу поддержки&10;,000ft Plans](mailto:support@10000ft.com).  

    c. Щелкните **Далее**


1. На странице **Настройка единого входа в 10,000ft Plans** выполните следующие действия и щелкните **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы получить данные единого входа, настроенные для вашего приложения, обратитесь в [службу поддержки&10;,000ft Plans](mailto:support@10000ft.com), прикрепите скачанный файл сертификата и укажите URL-адрес издателя, URL-адрес единого входа SAML и URL-адрес единого выхода.
3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-10000ft-plans-test-user"></a>Создание тестового пользователя 10,000ft Plans
Цель этого раздела — создать пользователя с именем Britta Simon в 10,000ft Plans. Приложение&10;,000ft Plans поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к 10,000ft Plans будет создан пользователь (если он еще не создан). 

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Certify.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к 10,000ft Plans.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в 10,000ft Plans, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **10,000ft Plans**.
   
    ![Настройка единого входа](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент 10,000ft Plans на панели доступа, вы автоматически войдете в приложение 10,000ft Plans.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_205.png


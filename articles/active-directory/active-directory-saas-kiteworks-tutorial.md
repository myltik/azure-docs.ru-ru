---
title: "Учебник по интеграции Azure Active Directory с Kiteworks | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Kiteworks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3be35d6c7efea5e5a784ee3c0d1965cc11bfcfe
ms.openlocfilehash: f64add6d742de24d0144db44e7c3885feb7a1139
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>Руководство по интеграции Azure Active Directory с Kiteworks
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Kiteworks. 

Интеграция Azure AD с приложением Kiteworks обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Kiteworks. 
* Вы можете включить автоматический вход пользователей в Kiteworks (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через Azure Active Directory. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Kiteworks, вам потребуется:

* подписка Azure AD;
* подписка на Kiteworks с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.  

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Kiteworks из коллекции 
2. Настройка и проверка единого входа Azure AD.

## <a name="add-kiteworks-from-the-gallery"></a>Добавление Kiteworks из коллекции
Чтобы настроить интеграцию Kiteworks с Azure AD, необходимо добавить Kiteworks из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kiteworks из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Kiteworks**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)
7. В области результатов выберите **Kiteworks** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Kiteworks с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Kiteworks соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kiteworks.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Kiteworks.

Чтобы настроить и проверить единый вход Azure AD в Kiteworks, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Kiteworks](#creating-a-kiteworks-test-user)** требуется для создания пользователя Britta Simon в Kiteworks, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Kiteworks. 

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Для настройки единого входа в приложение Kiteworks вам потребуется зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Kiteworks.  

**Чтобы настроить единый вход Azure AD в Kiteworks, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Kiteworks** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Kiteworks** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) 
3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) 
  1. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый для входа в приложение Kiteworks (например, *https://fabrikam.kiteworks.com/*).
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Kiteworks** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png)   
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. Войдите на корпоративный сайт Kiteworks с правами администратора.
6. На панели инструментов в верхней части экрана нажмите **Параметры**.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) 
7. В разделе **Authentication and Authorization** (Проверка подлинности и авторизация) щелкните **SSO Setup** (Настройка единого входа). 
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) 
8. На странице настройки единого входа выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png)   
  1. Установите флажок **Проверка подлинности с помощью единого входа**.
  2. Выберите **Initiate AuthnRequest**(Инициировать запрос на проверку подлинности).
  3. На классическом портале Azure на странице диалогового окна **Настройка единого входа в Kiteworks** скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **IDP Entity ID** (Идентификатор сущности IDP). 
  4. На классическом портале Azure на странице **Настройка единого входа в Kiteworks** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес службы единого входа**.
  5. На классическом портале Azure на странице **Настройка единого входа в Kiteworks** скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **URL-адрес службы единого выхода**.
  6. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат открытого ключа RSA** . 
  7. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]
10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png)  
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-kiteworks-test-user"></a>Создание тестового пользователя Kiteworks
Цель этого раздела — создать пользователя с именем Britta Simon в Kiteworks.

Приложение Kiteworks поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к Kiteworks будет создан пользователь (если он еще не создан).

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Kiteworks.
>  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Kiteworks, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Kiteworks, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Kiteworks**.
   
    ![Настройка единого входа](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент Kiteworks на панели доступа, вы автоматически войдете в приложение Kiteworks.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png








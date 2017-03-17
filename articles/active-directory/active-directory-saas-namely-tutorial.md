---
title: "Руководство по интеграции Azure Active Directory с Namely | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Namely."
services: active-directory
documentationcenter: 
author: jeevansd
manager: prasannas
editor: 
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 88f03ad7c8f0e6c5f8e1d6341f0c25eadc6dda9d
ms.openlocfilehash: ec9bba280377f8ccf7c1561b75c0396dcf5142b6
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Руководство по интеграции Azure Active Directory с Namely
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Namely.

Интеграция Azure AD с приложением Namely обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Namely. 
* Вы можете включить автоматический вход пользователей в Namely (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Namely, вам потребуется:

* подписка Azure AD;
* подписка на Namely с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Namely из коллекции 
2. Настройка и проверка единого входа Azure AD.

## <a name="add-namely-from-the-gallery"></a>Добавление Namely из коллекции
Чтобы настроить интеграцию Namely с Azure AD, необходимо добавить Namely из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Namely из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Namely**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)
7. В области результатов выберите **Namely** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Namely с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Namely соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Namely.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Namely.

Чтобы настроить и проверить единый вход Azure AD в Namely, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Namely](#creating-a-namely-test-user)** требуется для создания в Namely пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Namely. 

**Чтобы настроить единый вход Azure AD в Namely, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Namely** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Namely** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) 
3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) 
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый для входа в приложение Namely (например, *https://fabrikam.Namely.com/*).
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Namely** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) 
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. В другом окне браузера войдите на корпоративный сайт Namely с правами администратора.
6. На панели инструментов в верхней части экрана щелкните **Компания**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 
7. Перейдите на вкладку **Параметры** .
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 
8. Нажмите кнопку **SAML**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 
9. На странице **Параметры SAML** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) 
  1. Выберите команду **Enable SAML**(Включить SAML). 
  2. На классическом портале Azure на странице диалогового окна **Настройка единого входа в Namely** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Identity provider SSO url** (URL-адрес единого входа поставщика удостоверений). 
  3. Откройте сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Identity provider certificate** (Сертификат поставщика удостоверений).     
  4. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
    
     ![единого входа Azure AD][10]
11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
     ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png)  
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-namely-test-user"></a>Создание тестового пользователя Namely
Цель этого раздела — создать пользователя с именем Britta Simon в Namely.

**Чтобы создать пользователя с именем Britta Simon в Namely, выполните следующие действия.**

1. Войдите на корпоративный сайт Namely с правами администратора.
2. На панели инструментов в верхней части экрана щелкните **People**(Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 
3. Щелкните вкладку **Directory** (Каталог).
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 
4. Щелкните **Add New Person**(Добавить нового пользователя).
5. В диалоговом окне **Add New Person** (Добавление пользователя) выполните следующие действия.
  1. В текстовое поле **Имя** введите **Britta**.
  2. В текстовое поле **Фамилия** введите **Simon**.
  3. В текстовое поле **Электронный адрес** введите электронный адрес пользователя Britta Simon на классическом портале Azure.
  4. Щелкните **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Namely, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Namely, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **Namely**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Namely на панели доступа, вы автоматически войдете в приложение Namely.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png








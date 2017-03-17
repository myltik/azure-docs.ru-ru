---
title: "Руководство по интеграции Azure Active Directory с UserEcho | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении UserEcho."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: c6ea5ae5f51ac5584840a18b2d995f52297ebcf9
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Руководство. Интеграция Azure Active Directory с UserEcho
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением UserEcho.

Интеграция Azure AD с приложением UserEcho обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к приложению UserEcho. 
* Вы можете включить автоматический вход пользователей в UserEcho (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением UserEcho, вам потребуется следующее:

* подписка Azure AD;
* подписка на UserEcho с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.  

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление UserEcho из коллекции. 
2. Настройка и проверка единого входа Azure AD.

## <a name="add-userecho-from-the-gallery"></a>Добавление UserEcho из коллекции
Чтобы настроить интеграцию UserEcho с Azure AD, необходимо добавить UserEcho из коллекции в список управляемых приложений SaaS.

**Чтобы добавить UserEcho из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **UserEcho**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)
7. В области результатов выберите **UserEcho** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в UserEcho с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в UserEcho соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в UserEcho.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в UserEcho.

Чтобы настроить и проверить единый вход Azure AD в UserEcho, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя UserEcho](#creating-a-userecho-test-user)** требуется для создания в UserEcho пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении UserEcho. 

**Чтобы настроить единый вход Azure AD в UserEcho, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **UserEcho** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в UserEcho?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) 
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый для входа в приложение UserEcho (например, *https://fabrikam.UserEcho.com/*).
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в UserEcho** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) 
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. В другом окне браузера войдите на корпоративный сайт UserEcho с правами администратора.
6. На панели инструментов вверху щелкните имя пользователя, чтобы развернуть меню, а затем щелкните **Setup**(Настройка).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
7. Щелкните **Integrations**(Интеграция).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 
8. Щелкните **Website** (Веб-сайт), а затем — **Single sign-on (SAML2)** (Единый вход (SAML2)).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 
9. На странице **Single sign-on (SAML)** (Единый вход (SAML)) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) 
  1. В поле **SAML-enabled** (Включить SAML) выберите **Yes** (Да). 
  2. На классическом портале Azure на странице диалогового окна "Настройка единого входа в UserEcho" скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес единого входа SAML**.
  3. На классическом портале Azure на странице диалогового окна "Настройка единого входа в UserEcho" скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес удаленного выхода**. 
  4. Откройте скачанный сертификат в блокноте, а затем скопируйте и вставьте его содержимое в текстовое поле **X.509 Certificate** (Сертификат X.509).    
  5. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
    
     ![единого входа Azure AD][10]
11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
     ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)  
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-userecho-test-user"></a>Создание тестового пользователя UserEcho
Цель этого раздела — создать в приложении UserEcho пользователя с именем Britta Simon.

**Чтобы создать пользователя с именем Britta Simon в UserEcho, выполните следующие действия.**

1. Войдите на корпоративный сайт UserEcho с правами администратора.
2. На панели инструментов вверху щелкните имя пользователя, чтобы развернуть меню, а затем щелкните **Setup**(Настройка).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
3. Щелкните **Users** (Пользователи), чтобы развернуть раздел **Users** (Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) 
4. Выберите раздел **Пользователи**.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) 
5. Щелкните **Invite a new user**(Пригласить нового пользователя).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)
6. В диалоговом окне **Invite a new user** (Приглашение нового пользователя) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) 
  1. В текстовом поле **Name** (Имя) введите **Britta Simon**.
  2. В текстовое поле **Электронный адрес** введите электронный адрес пользователя Britta Simon на классическом портале Azure.
  3. Нажмите кнопку **Пригласить**.

Пользователю Britta будет отправлено приглашение, с помощью которого можно начать работу с приложением UserEcho. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к UserEcho, чтобы он мог использовать единый вход Azure AD.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению UserEcho, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **UserEcho**.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент UserEcho на панели доступа, вы автоматически войдете в приложение UserEcho.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png








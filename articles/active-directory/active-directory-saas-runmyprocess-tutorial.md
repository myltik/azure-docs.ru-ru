---
title: "Руководство по интеграции Azure Active Directory с RunMyProcess | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в RunMyProcess."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8aeac420b3848a270a3a9a325c9db0f1569a33e6
ms.openlocfilehash: 2f6bcdd9ea97494d3d12ea4ffd541b4b95a91c4f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Учебник. Интеграция Azure Active Directory с RunMyProcess
Цель этого руководства — показать, как интегрировать RunMyProcess с Azure Active Directory (Azure AD).

Интеграция RunMyProcess с приложением Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к RunMyProcess.
* Вы можете включить автоматический вход пользователей в RunMyProcess (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с клиентом RunMyProcess, вам потребуется:

* подписка Azure AD;
* подписка на RunMyProcess с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения RunMyProcess из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-runmyprocess-from-the-gallery"></a>Добавление приложения RunMyProcess из коллекции
Чтобы настроить интеграцию RunMyProcess с Azure AD, необходимо добавить RunMyProcess из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RunMyProcess из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **RunMyProcess**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. В области результатов выберите **RunMyProcess** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении RunMyProcess с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в RunMyProcess соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RunMyProcess.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в RunMyProcess.

Чтобы настроить и проверить единый вход Azure AD в RunMyProcess, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RunMyProcess](#creating-a-runmyprocess-test-user)** требуется для создания в RunMyProcess пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении RunMyProcess.

**Чтобы настроить единый вход Azure AD в RunMyProcess, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **RunMyProcess** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в RunMyProcess?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://live.runmyprocess.com/live/<tenant id>`. 
  2. Нажмите кнопку **Далее**.
    >[!NOTE]
    >Обратите внимание, что вместо этого значения нужно указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь в службу поддержки RunMyProcess по адресу <mailto:support@runmyprocess.com>.
    >  
4. На странице **Настройка единого входа в RunMyProcess** щелкните **Скачать сертификат**, а затем сохраните файл на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. В другом окне веб-браузера войдите на корпоративный сайт RunMyProcess с правами администратора.
6. На панели навигации слева щелкните **Учетная запись** и выберите **Конфигурация**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. Перейдите в раздел **Authentication method** (Метод проверки подлинности) и выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
  1. Для параметра **Method** (Метод) выберите значение **SSO with Samlv2** (Единый вход с помощью SAML версии 2). 
  2. В текстовое поле **SSO redirect** (Перенаправление SSO) вставьте значение **URL-адреса единого входа SAML** из мастера настройки приложения в Azure AD.
  3. В текстовое поле **Logout redirect** (Перенаправление выхода) введите значение **URL-адреса службы единого выхода** из мастера настройки приложения в Azure AD.
  4. В текстовое поле **Name Id Format** (Формат идентификатора имени) вставьте значение **формата идентификатора имени** из мастера настройки приложения в Azure AD.
  5. Скопируйте содержимое скачанного файла сертификата и вставьте его в **соответствующее** текстовое поле. 
  6. Щелкните значок **Сохранить**.
8. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png)  
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png)  
 1. Запишите значение поля **Новый пароль**.
 2. Нажмите **Завершено**.   

### <a name="create-a-runmyprocess-test-user"></a>Создание тестового пользователя RunMyProcess
Чтобы пользователи Azure AD могли выполнять вход в RunMyProcess, они должны быть подготовлены для RunMyProcess. В случае с RunMyProcess подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите на веб-сайт RunMyProcess компании в качестве администратора.
2. Щелкните **Учетная запись** и выберите **Пользователи** на левой панели навигации. Затем нажмите кнопку **Новый пользователь**.
   
   ![Новый пользователь](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Новый пользователь")
3. В разделе **Параметры пользователя** сделайте следующее:
   
   ![Профиль](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Профиль")   
 1. Заполните текстовые поля **Name** (Имя) и **E-mail** (Электронный адрес) данными действующей учетной записи AAD, которую необходимо подготовить. 
 2. Выберите значения параметров **IDE language** (Язык интегрированной среды разработки), **Language** (Язык) и **Profile** (Профиль). 
 3. Установите флажок **Отправить сообщение о создании учетной записи**. 
 4. Щелкните **Сохранить**.
   
   >[!NOTE]
   >Вы можете использовать любые другие инструменты создания учетных записей пользователя RunMyProcess или API, предоставляемые RunMyProcess для подготовки учетных записей пользователя AAD. 
   > 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к RunMyProcess, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению RunMyProcess, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **RunMyProcess**.
   
    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку RunMyProcess на панели доступа, вы автоматически войдете в приложение RunMyProcess.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png


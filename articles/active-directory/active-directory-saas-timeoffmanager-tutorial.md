---
title: Руководство по интеграции Azure Active Directory с TimeOffManager | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 462a77e56f2dc28a3a3258ab44a1486a4dd257e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352741"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Учебник. Интеграция Azure Active Directory с TimeOffManager

В этом руководстве описано, как интегрировать TimeOffManager с Azure Active Directory (Azure AD).

Интеграция TimeOffManager с Azure AD обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к TimeOffManager.
- Вы можете включить автоматический вход пользователей в TimeOffManager (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с TimeOffManager, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа TimeOffManager

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TimeOffManager из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="add-timeoffmanager-from-the-gallery"></a>Добавление TimeOffManager из коллекции
Чтобы настроить интеграцию TimeOffManager с Azure AD, необходимо добавить TimeOffManager из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TimeOffManager из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **TimeOffManager**, выберите **TimeOffManager** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Добавление из коллекции](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в TimeOffManager с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в TimeOffManager соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TimeOffManager.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TimeOffManager.

Чтобы настроить и проверить единый вход Azure AD в TimeOffManager, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TimeOffManager](#create-a-timeoffmanager-test-user)** требуется для того, чтобы в TimeOffManager существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TimeOffManager.

**Чтобы настроить единый вход Azure AD в TimeOffManager, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **TimeOffManager** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Вход на основе SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TimeOffManager** сделайте следующее.

     ![Раздел "Домены и URL-адреса приложения TimeOffManager"](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический URL-адрес ответа. Получить это значение можно **на странице параметров единого входа**, как описано далее в руководстве. Кроме того, можно обратиться к [группе поддержки TimeOffManager](http://www.timeoffmanager.com/contact-us.aspx).
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. В этом разделе показано, как разрешить пользователям проходить аутентификацию в TimeOffManager со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.
    
    Приложение TimeOffManger ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример.

    ![Атрибуты токена SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "Атрибуты токена SAML")
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | Firstname |User.givenname |
    | Lastname |User.surname |
    | Email |User.mail |
    
    a.  Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
    
    ![Атрибуты токена SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "Атрибуты токена SAML")
    
    ![Атрибуты токена SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "Атрибуты токена SAML")
    
    Б.  В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
    
    c.  В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
    
    d.  Нажмите кнопку **ОК**.
    
6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация TimeOffManager** щелкните **Настроить TimeOffManager**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Раздел "Конфигурация TimeOffManager"](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TimeOffManager в качестве администратора.

9. Выберите **Account \> Account Options \> Single Sign-On Settings** ("Учетная запись" > "Параметры учетной записи" > "Параметры единого входа").
   
   ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/ic795917.png "Параметры единого входа")
7. В разделе **Параметры единого входа** сделайте следующее:
   
   ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/ic795918.png "Параметры единого входа")
   
   a. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **Сертификат X.509** .
   
   Б. В текстовое поле **Idp Issuer** (Издатель IdP) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.
   
   c. В текстовое поле **IdP Endpoint URL** (URL-адрес конечной точки IdP) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
   
   d. Для параметра **Enforce SAML** (Применить SAML) выберите значение **No** (Нет).
   
   д. Для параметра **Auto-Create Users** (Автосоздание пользователей) выберите значение **Yes** (Да).
   
   f. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
   
   ж. Нажмите кнопку **Save Changes** (Сохранить изменения).

11. На странице **Single Sign on settings** (Параметры единого входа) скопируйте значение **Assertion Consumer Service URL** (URL-адрес службы обработчика утверждений) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения TimeOffManager** на портале Azure. 

      ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/ic795915.png "Параметры единого входа")

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    !["Пользователи и группы" > "Все пользователи"](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Создание тестового пользователя TimeOffManager

Чтобы пользователи Azure AD могли входить в систему TimeOffManager, их необходимо подготовить для нее.  

TimeOffManager поддерживает автоматическую подготовку пользователей. С вашей стороны никакие действия не требуются.  

Пользователи автоматически добавляются при первом входе с помощью единого входа.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей TimeOffManager или API, предоставляемые TimeOffManager для подготовки учетных записей пользователей Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TimeOffManager.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в TimeOffManager, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **TimeOffManager**.

    ![TimeOffManager в списке приложений](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "TimeOffManager" на панели доступа, вы автоматически войдете в приложение TimeOffManager. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с Canvas LMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Canvas LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 56fe4679448fec1db67f826ff70cf8768699e507
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Руководство по интеграции Azure Active Directory с Canvas LMS

В этом учебнике описано, как интегрировать Canvas с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Canvas обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Canvas.
- Вы можете включить автоматический вход пользователей в Canvas (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Canvas, вам потребуется:

- подписка Azure AD;
- подписка Canvas с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Canvas из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-canvas-from-the-gallery"></a>Добавление Canvas из коллекции
Чтобы настроить интеграцию Canvas с Azure AD, необходимо добавить Canvas из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Canvas из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Canvas**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. На панели результатов выберите **Canvas** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Canvas с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Canvas соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Canvas.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Canvas.

Чтобы настроить и проверить единый вход Azure AD в Canvas, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Canvas](#creating-a-canvas-test-user)** нужно для того, чтобы в Canvas также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Canvas.

**Чтобы настроить единый вход Azure AD в Canvas, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Canvas** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Canvas** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.instructure.com`

    Б. В текстовом поле **Идентификатор** введите значение в следующем формате: `https://<tenant-name>.instructure.com/saml2`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки Canvas](https://community.canvaslms.com/community/help). 
 
4. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток**.

    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Canvas** щелкните **Настроить Canvas**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес изменения пароля, URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Canvas в качестве администратора.

8. Выберите **Курсы \> Управляемые учетные записи \> Microsoft**.
   
    ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

9. На панели навигации слева выберите раздел **Проверка подлинности** и нажмите кнопку **Add New SAML Config** (Добавить новую конфигурацию SAML).
   
    ![Аутентификация](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Аутентификация")

10. На странице "Текущая интеграция" выполните следующие действия.
   
    ![Текущая интеграция](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Текущая интеграция")

    a. В текстовое поле **IdP Entity ID** (Идентификатор сущности IdP) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    Б. В текстовое поле **Log On URL** (URL-адрес входа) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. В текстовое поле **Log Out URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    d. В текстовое поле **Change Password Link** (Ссылка для изменения пароля) вставьте **URL-адрес изменения пароля**, скопированный на портале Azure. 

    д. В текстовое поле **Certificate Fingerprint** (Отпечаток сертификата) вставьте значение **Отпечаток**, которое вы скопировали на портале Azure.      
        
    f. В списке **Login Attribute** (Атрибут входа) выберите значение **NameID**.

    ж. В списке **Identifier Format** (Формат идентификатора) выберите значение **emailAddress**.

    h. Нажмите кнопку **Сохранить параметры проверки подлинности**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-canvas-test-user"></a>Создание тестового пользователя Canvas

Чтобы пользователи Azure AD могли выполнить вход в Canvas, они должны быть подготовлены в Canvas.

В случае Canvas подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход в клиент **Canvas** .

2. Выберите **Курсы \> Управляемые учетные записи \> Microsoft**.
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")

3. Выберите раздел **Пользователи**.
   
   ![Пользователи](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Пользователи")

4. Нажмите кнопку **Add New User**(Добавить нового пользователя).
   
   ![Пользователи](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Пользователи")

5. На странице диалогового окна "Добавление пользователя" выполните следующие действия.
   
   ![Добавление пользователя](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Добавление пользователя")
   
   a. В текстовое поле **Full Name** (Полное имя) введите имя, например **BrittaSimon**.

   Б. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

   c. В текстовом поле **Login** (Имя для входа) введите адрес электронной почты пользователя в Azure AD, например **brittasimon@contoso.com**.

   d. Установите флажок **Email the user about this account creation**(Сообщить пользователю о создании этой учетной записи по электронной почте).

   д. Нажмите кнопку **Add User**(Добавить пользователя).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Canvas или API, предоставляемые Canvas для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Canvas.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Canvas, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Canvas**.

    ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Canvas" на панели доступа, вы автоматически войдете в приложение Canvas.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png


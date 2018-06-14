---
title: Руководство по интеграции Azure Active Directory с SAML SSO for Jira by resolution GmbH | Документация Майкрософт
description: Узнайте, как настроить единый вход для Azure Active Directory и SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: b0835d89786e2d389b845c8a4bdbbfb63c0bbcd3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352367"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Руководство по интеграции Azure Active Directory с SAML SSO for Jira by resolution GmbH

В этом руководстве описано, как интегрировать SAML SSO for Jira by resolution GmbH с Azure Active Directory (Azure AD).

Интеграция SAML SSO for Jira by resolution GmbH с Azure AD предоставляет следующие преимущества.

- Можно управлять доступом пользователей Azure AD к SAML SSO for Jira by resolution GmbH.
- Можно включить автоматический вход пользователей в SAML SSO for Jira by resolution GmbH (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Для настройки интеграции Azure AD с SAML SSO for Jira by resolution GmbH требуется:

- подписка Azure AD;
- подписка SAML SSO for Jira by resolution GmbH с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAML SSO for Jira by resolution GmbH из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Добавление SAML SSO for Jira by resolution GmbH из коллекции
Чтобы настроить интеграцию SAML SSO for Jira by resolution GmbH в Azure AD, необходимо добавить SAML SSO for Jira by resolution GmbH из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAML SSO for Jira by resolution GmbH из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SAML SSO for Jira by resolution GmbH**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. На панели результатов выберите **SAML SSO for Jira by resolution GmbH** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SAML SSO for Jira by resolution GmbH с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAML SSO for Jira by resolution GmbH соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAML SSO for Jira by resolution GmbH.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAML SSO for Jira by resolution GmbH.

Чтобы настроить и проверить единый вход Azure AD в SAML SSO for Jira by resolution GmbH, требуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAML SSO for Jira by resolution GmbH](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)** требуется, чтобы создать в SAML SSO for Jira by resolution GmbH пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении SAML SSO for Jira by resolution GmbH.

**Чтобы настроить единый вход Azure AD в SAML SSO for Jira by resolution GmbH, выполните следующее.**

1. На портале Azure на странице интеграции с приложением **SAML SSO for Jira by resolution GmbH** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **IdP**, то в разделе **Домены и URL-адреса приложения SAML SSO for Jira by resolution GmbH** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Обратитесь к [группе поддержки SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support) для получения этих значений. 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. В другом окне браузера войдите на **портал администрирования SAML SSO for Jira by resolution GmbH** с правами администратора.

8. Наведите указатель мыши на шестеренку и щелкните **Add-ons** (Надстройки).
    
    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. Вы перейдете на страницу доступа с правами администратора. Введите **пароль** и нажмите кнопку **Confirm** (Подтвердить).

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. На вкладке "Add-ons" (Надстройки) щелкните **Find new add-ons** (Найти новые надстройки). Найдите подключаемый модуль **SAML Single Sign On (SSO) for Jira** и нажмите кнопку **Install** (Установить), чтобы установить новый подключаемый модуль SAML.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. Начнется установка подключаемого модуля. Нажмите кнопку **Закрыть**

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. Нажмите кнопку **Управление**.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. На странице **SAML SingleSignOn Plugin Configuration** (Конфигурация подключаемого модуля единого входа SAML) нажмите кнопку **Add new IdP** (Добавить новый поставщик удостоверений), чтобы настроить параметры поставщика удостоверений.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. На странице **Choose your SAML Identity Provider** (Выбор поставщика удостоверений SAML) выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon5a.png)
 
    a. Для типа поставщика удостоверений выберите значение **Azure AD**.
    
    Б. Добавьте **имя** поставщика удостоверений (например, Azure AD).
    
    c. Добавьте **описание** поставщика удостоверений (например, Azure AD).
    
    d. Нажмите кнопку **Далее**.
    
16. На странице **Identity provider configuration** (Настройка поставщика удостоверений) нажмите кнопку **Next** (Далее).

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon5b.png)

17. На странице **Import SAML IdP Metadata** (Импорт метаданных поставщика удостоверений SAML) выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon5c.png)

    a. Нажмите кнопку **Load File** (Загрузить файл) и выберите XML-файл метаданных, который вы скачали на шаге 5.

    Б. Нажмите кнопку **Import** (Импортировать).
    
    c. Дождитесь завершения импорта.
    
    d. Нажмите кнопку **Next** (Далее).
    
18. На странице **User ID attribute and transformation** (Атрибут и преобразование идентификатора пользователя) нажмите кнопку **Next** (Далее).

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon5d.png)
    
19. На странице **User creation and update** (Создание и изменение пользователя) нажмите кнопку **Save & Next** (Сохранить и продолжить), чтобы сохранить параметры.   
    
    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon6a.png)
    
20. На странице **Test your settings** (Проверка параметров) нажмите кнопку **Skip test & configure manually** (Пропустить проверку и настроить вручную), чтобы не выполнять проверку на этом этапе. Проверка будет выполнена на одном из следующих этапов, и для этого потребуется выполнить некоторые настройки на портале Azure. 
    
    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon6b.png)
    
21. В открывшемся окне с сообщением **Skipping the test means...** (Если вы пропустите проверку...) нажмите кнопку **OK**.
    
    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/addon6c.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Создание тестового пользователя SAML SSO for Jira by resolution GmbH

Чтобы пользователи Azure AD могли входить в SAML SSO for Jira by resolution GmbH, их необходимо подготовить в SAML SSO for Jira by resolution GmbH.  
Подготовка в SAML SSO for Jira by resolution GmbH выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на свой корпоративный сайт SAML SSO for Jira by resolution GmbH с правами администратора.

2. Наведите указатель мыши на шестеренку и щелкните **User management** (Управление пользователями).

    ![Добавление сотрудника](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. Вы будете перенаправлены на страницу доступа с правами администратора. Введите **пароль** и нажмите кнопку **Confirm** (Подтвердить).

    ![Добавление сотрудника](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. В разделе **User management** (Управление пользователями) щелкните **Create user** (Создать пользователя).

    ![Добавление сотрудника](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. На странице **Create New User** (Создание пользователя) выполните следующие действия.

    ![Добавление сотрудника](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. В текстовом поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    Б. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.

    c. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя.

    д. Щелкните **Create user** (Создать пользователя).   

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SAML SSO for Jira by resolution GmbH.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAML SSO for Jira by resolution GmbH, выполните следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **SAML SSO for Jira by resolution GmbH**.

    ![Настройка единого входа](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAML SSO for Jira by resolution GmbH на панели доступа, вы должны автоматически войти в свое приложение SAML SSO for Jira by resolution GmbH.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png


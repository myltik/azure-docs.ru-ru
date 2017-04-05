---
title: "Руководство. Интеграция Azure Active Directory с Citrix ShareFile | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Руководство. Интеграция Azure Active Directory с Citrix ShareFile

В этом руководстве описано, как интегрировать Citrix ShareFile с Azure Active Directory (Azure AD).

Интеграция Azure AD с Citrix ShareFile обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Citrix ShareFile.
- Вы можете включить автоматический вход пользователей в CloudPassage (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Citrix ShareFile, вам потребуется:

- подписка Azure AD;
- подписка Citrix ShareFile с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. добавление Citrix ShareFile из коллекции;
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-citrix-sharefile-from-the-gallery"></a>Добавление Citrix ShareFile из коллекции
Чтобы настроить интеграцию Citrix ShareFile с Azure AD, вам нужно добавить Citrix ShareFile из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Citrix ShareFile из коллекции, выполните следующие действия.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Citrix ShareFile**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. На панели результатов выберите **Citrix ShareFile** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Citrix ShareFile с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Citrix ShareFile соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Citrix ShareFile.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Citrix ShareFile.

Чтобы настроить и проверить единый вход Azure AD в Citrix ShareFile, вам нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Citrix ShareFile](#creating-a-citrix-sharefile-test-user)** требуется для создания в Citrix ShareFile пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описывается, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Citrix ShareFile.

**Чтобы настроить единый вход Azure AD в Citrix ShareFile, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **Citrix ShareFile** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. В разделе **Домены и URL-адреса приложения Citrix ShareFile** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.sharefile.com/saml/login`.    

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Если вам не известны эти URL-адреса, введите примеры URL-адресов из шаблона. Фактические URL-адреса вы получите после завершения шага 13.

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. В разделе **конфигурации Citrix ShareFile** щелкните **Настройка Citrix ShareFile**, чтобы открыть окно **Настройка входа**.

    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Citrix ShareFile в качестве администратора.

7. На панели инструментов в верхней части экрана нажмите **Администратор**.

8. В левой области навигации нажмите **Настройка единого входа**.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. На странице диалогового окна **Single Sign-On/ SAML 2.0 Configuration** (Настройка единого входа или SAML 2.0) в разделе **Основные параметры** выполните следующие действия:

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    а. Выберите команду **Enable SAML**(Включить SAML).

    b. В текстовое поле **Your IDP Issuer/Entity ID** (Идентификатор издателя IdP или сущности) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML) из окна настройки приложения Azure AD.

    c. Нажмите **Change** (Изменить) рядом с полем **X.509 Certificate** (Сертификат X.509) и передайте загруженный файл сертификата. 

    d. В текстовом поле **URL-адрес для входа** введите значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из окна настройки приложения Azure AD.

    д. В текстовое поле **URL-адреса выхода** введите значение **URL-адреса выхода** из окна настройки приложения Azure AD.

10. Нажмите **Сохранить** на портале управления Citrix ShareFile.
    
 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-citrix-sharefile-test-user"></a>Создание тестового пользователя Citrix ShareFile

Чтобы пользователи Azure AD могли выполнить вход в Citrix ShareFile, они должны быть подготовлены для Citrix ShareFile. В случае с Citrix ShareFile подготовка выполняется вручную.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1. Войдите на корпоративный сайт Citrix ShareFile в качестве администратора.

2. На панели инструментов в верхней части экрана щелкните **Manage Users** (Управление пользователями). Теперь перейдите к разделу **Manage Users Home** (Домашняя страница управления пользователями) и щелкните **Create Employee** (Создать сотрудника).

    ![Новый пользователь](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "Новый пользователь")

3. В разделе **Basic Information** (Базовые сведения) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "Новый пользователь")

    а. В текстовое поле **Email Address** (Электронная почта) введите адрес электронной почты учетной записи Britta Simon.  

    b. В текстовом поле **Имя** введите **Britta**.  

    c. В текстовом поле **Фамилия** введите **Simon**.

4. Нажмите кнопку **Add User**(Добавить пользователя).

    > [!NOTE]
    > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетной записи пользователя Citrix ShareFile или API, предоставляемые Citrix ShareFile для подготовки учетных записей пользователей AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как настроить для пользователя Britta Simon единый вход Azure, предоставив доступ к Citrix ShareFile.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Citrix ShareFile, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Citrix ShareFile**.

    ![Настройка единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix ShareFile на панели доступа, вы автоматически войдете в приложение Citrix ShareFile.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png

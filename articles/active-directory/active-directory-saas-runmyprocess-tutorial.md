---
title: Руководство по интеграции Azure Active Directory с RunMyProcess | Документация Майкрософт
description: Сведения о настройке единого входа Azure Active Directory в RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 9232cbcef0bb445e8fc09475feaa2251afd3d3fb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Учебник. Интеграция Azure Active Directory с RunMyProcess

В этом руководстве описано, как интегрировать RunMyProcess с Azure Active Directory (Azure AD).

Интеграция RunMyProcess с приложением Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к RunMyProcess.
- Вы можете включить автоматический вход пользователей в RunMyProcess (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с клиентом RunMyProcess, вам потребуется:

- подписка Azure AD;
- подписка RunMyProcess с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной версии среды Azure AD, вы можете получить бесплатную пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление приложения RunMyProcess из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-runmyprocess-from-the-gallery"></a>Добавление приложения RunMyProcess из коллекции
Чтобы настроить интеграцию RunMyProcess с Azure AD, необходимо добавить RunMyProcess из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RunMyProcess из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **RunMyProcess**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_search.png)

5. На панели результатов выберите **RunMyProcess** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в RunMyProcess с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в RunMyProcess соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RunMyProcess.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в RunMyProcess.

Чтобы настроить и проверить единый вход Azure AD в RunMyProcess, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RunMyProcess](#creating-a-runmyprocess-test-user)** требуется для того, чтобы в RunMyProcess существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении RunMyProcess.

**Чтобы настроить единый вход Azure AD в RunMyProcess, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **RunMyProcess** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

3. В разделе **Домены и URL-адреса приложения RunMyProcess** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки клиентов RunMyProcess](mailto:support@runmyprocess.com). 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_400.png)

6. В разделе **Настройка RunMyProcess** щелкните **Настроить RunMyProcess**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт RunMyProcess с правами администратора.

8. На панели навигации слева щелкните **Учетная запись** и выберите **Конфигурация**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

9. Перейдите в раздел **Authentication method** (Метод проверки подлинности) и выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Для параметра **Method** (Метод) выберите значение **SSO with Samlv2** (Единый вход с помощью SAML версии 2). 

    Б. В текстовое поле **SSO redirect** (Перенаправление для единого входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. В текстовое поле **Logout redirect** (Перенаправление для выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    d. В текстовое поле **Name Id Format** (Формат идентификатора имени) введите следующий **формат**: **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    д. Скопируйте содержимое скачанного файла сертификата и вставьте его в **соответствующее** текстовое поле. 
 
    f. Щелкните значок **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Создание тестового пользователя RunMyProcess

Чтобы пользователи Azure AD могли выполнять вход в RunMyProcess, они должны быть подготовлены в RunMyProcess. В случае с RunMyProcess подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт RunMyProcess компании в качестве администратора.

2. Щелкните **Учетная запись** и выберите **Пользователи** на левой панели навигации. Затем нажмите кнопку **Новый пользователь**.
   
    ![Новый пользователь](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Новый пользователь")

3. В разделе **Параметры пользователя** сделайте следующее:
   
    ![Профиль](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Профиль") 
  
    a. Заполните текстовые поля **Name** (Имя) и **E-mail** (Электронная почта) данными действительной учетной записи Azure AD, которую необходимо подготовить. 

    Б. Выберите значения параметров **IDE language** (Язык интегрированной среды разработки), **Language** (Язык) и **Profile** (Профиль). 

    c. Установите флажок **Отправить сообщение о создании учетной записи**. 

    d. Выберите команду **Сохранить**.
   
    >[!NOTE]
    >Вы можете использовать любые другие инструменты создания учетных записей пользователя RunMyProcess или API, предоставляемые RunMyProcess для подготовки учетных записей пользователя AAD. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к RunMyProcess.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению RunMyProcess, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **RunMyProcess**.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку RunMyProcess на панели доступа, вы автоматически войдете в приложение RunMyProcess.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png


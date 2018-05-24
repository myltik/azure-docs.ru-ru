---
title: Руководство по интеграции Azure Active Directory с xMatters OnDemand | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2696be33c97b411a9f4809f0ff6ca56cd2fe6080
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Руководство по интеграции Azure Active Directory с xMatters OnDemand

В этом руководстве описано, как интегрировать xMatters OnDemand с Azure Active Directory (Azure AD).

Интеграция xMatters OnDemand с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к xMatters OnDemand.
- Вы можете включить автоматический вход пользователей в xMatters OnDemand (единый вход) с учетной записью Azure AD
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с xMatters OnDemand, вам потребуется:

- подписка Azure AD;
- подписка xMatters OnDemand с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление xMatters OnDemand из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Добавление xMatters OnDemand из коллекции
Чтобы настроить интеграцию xMatters OnDemand с Azure AD, необходимо добавить xMatters OnDemand из коллекции в список управляемых приложений SaaS.

**Чтобы добавить xMatters OnDemand из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **xMatters OnDemand**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. На панели результатов выберите **xMatters OnDemand** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в xMatters OnDemand с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в xMatters OnDemand соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в xMatters OnDemand.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в xMatters OnDemand.

Чтобы настроить и проверить единый вход Azure AD в xMatters OnDemand, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя xMatters OnDemand](#creating-a-xmatters-ondemand-test-user)** требуется для того, чтобы в xMatters OnDemand существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении xMatters OnDemand.

**Чтобы настроить единый вход Azure AD в xMatters OnDemand, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **xMatters OnDemand** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. В разделе **Домены и URL-адреса xMatters OnDemand** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:   
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки xMatters OnDemand](https://www.xmatters.com/company/contact-us/).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на локальном компьютере как **c:\\XMatters OnDemand.cer**.

    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)
    
    > [!IMPORTANT]
    > Вам необходимо отправить сертификат [группе поддержки xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Служба поддержки xMatters должна загрузить сертификат, чтобы вы могли завершить настройку единого входа. 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация xMatters OnDemand** щелкните **Настроить xMatters OnDemand**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный сайт xMatters OnDemand в качестве администратора.

8. В верхней части панели инструментов щелкните **Admin** (Администратор) и выберите **Company Details** (Сведения о компании) на панели навигации слева.
   
    ![Администратор](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Администратор")

9. На странице **Настройка SAML** сделайте следующее:
   
    ![Настройка SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Настройка SAML")
   
    a. Выберите **Включить SAML**.
   
    Б. Вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure, в поле **Identity Provider ID** (Идентификатор поставщика удостоверений).
   
    c. В текстовое поле **Single Sign On URL** (URL-адрес единого входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    d. В текстовое поле **Single Logout URL** (URL-адрес единого выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
   
    д. В верхней части страницы «Информация о компании» нажмите кнопку **Сохранить изменения**.
    
    ![Сведения о компании](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Сведения о компании")

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-xmatters-ondemand-test-user"></a>Создание тестового пользователя xMatters OnDemand

Чтобы пользователи Azure AD могли выполнять вход в xMatters OnDemand, они должны быть подготовлены в xMatters OnDemand. В случае с XMatters OnDemand подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Войдите в клиент **xMatters OnDemand** .

2.  Откройте вкладку **Пользователи**, а затем выберите **Добавить пользователя**.
  
    ![Пользователи](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Пользователи")

3. В разделе **Добавление пользователя** сделайте следующее:
   
    ![Добавление пользователя](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Добавление пользователя")

    a. Установите флажок **Активно**.

    Б. В текстовое поле **User ID** (Идентификатор пользователя) введите идентификатор пользователя, например Brittasimon@contoso.com.
   
    c. В текстовое поле **First Name** (Имя) введите имя пользователя, например Britta.

    d. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.
    
    д. В текстовое поле **Site** (Сайт) введите действительный сайт учетной записи Azure AD, которую необходимо подготовить.
    
    f. Выберите команду **Сохранить**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к xMatters OnDemand.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в xMatters OnDemand, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **xMatters OnDemand**.

    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "xMatters OnDemand" на панели доступа, вы автоматически войдете в приложение xMatters OnDemand.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_203.png


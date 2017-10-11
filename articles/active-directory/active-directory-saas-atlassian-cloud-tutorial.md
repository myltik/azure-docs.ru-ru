---
title: "Руководство по интеграции Azure Active Directory с Atlassian Cloud | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в приложении Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 2891838b56dd15cb5f97dcae391770143a80c781
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Руководство по интеграции Azure Active Directory с Atlassian Cloud

В этом руководстве описано, как интегрировать приложение Atlassian Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Atlassian Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Atlassian Cloud.
- Вы можете включить автоматический вход пользователей в Atlassian Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Atlassian Cloud, вам потребуется:

- подписка Azure AD;
- подписка Atlassian Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. добавление Atlassian Cloud из коллекции;
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции
Чтобы настроить интеграцию Atlassian Cloud с Azure AD, необходимо добавить Atlassian Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Atlassian Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_search.png)

5. На панели результатов выберите **Atlassian Cloud** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Atlassian Cloud с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD необходимо знать, какой пользователь в Atlassian Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Atlassian Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Atlassian Cloud.

Чтобы настроить и проверить единый вход Azure AD в Atlassian Cloud, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Atlassian Cloud](#creating-an-atlassian-cloud-test-user)** требуется для создания в Atlassian Cloud пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале и настроить его в приложении Atlassian Cloud.

**Чтобы настроить единый вход Azure AD в Atlassian Cloud, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Atlassian Cloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Atlassian Cloud** выполните указанные ниже действия.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<instancename>.atlassian.net/admin/saml/edit`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем виде: `https://id.atlassian.com/login/saml/acs`

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса единого входа. Точные значения можно получить на экране настройки SAML для Atlassian Cloud.
 
5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. В разделе **Настройка Atlassian Cloud** щелкните **Настроить Atlassian Cloud**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

7. Чтобы настроить единый вход для вашего приложения, войдите на портал Atlassian с правами администратора.

8. В левой области навигации в разделе Authentication (Проверка подлинности) выберите пункт **Domains** (Домены).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    а. В текстовом поле введите имя домена, а затем нажмите кнопку **Add domain** (Добавить домен).
        
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. Чтобы проверить домен, нажмите кнопку **Verify** (Проверить). 

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. Скачайте HTML-файл проверки домена, передайте его в корневую папку веб-сайта в домене и нажмите кнопку **Verify domain** (Проверить домен).
    
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    г) После проверки домена значение в поле **Status** (Состояние) изменится на **Verified** (Проверено).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

9. В левой панели навигации щелкните **SAML**.
 
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

10. Создайте конфигурацию SAML и задайте параметры поставщика удостоверений.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    а. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **Идентификатор сущности SAML**, скопированное на портале Azure.

    b. В текстовое поле **Identity Provider SSO URL** (URL-адрес единого входа поставщика удостоверений) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. Откройте сертификат, скачанный с портала Azure, скопируйте значения без строк Begin и End и вставьте их в поле **Public X509 certificate** (Общий сертификат X509).
    
    г) Чтобы сохранить параметры, нажмите кнопку **Save Configuration** (Сохранить конфигурацию).
     
11. В параметрах Azure AD задайте правильный URL-адрес идентификатора.
  
    а. Скопируйте **идентификатор удостоверения поставщика услуг** на экране SAML и вставьте его в Azure AD в качестве значения поля **Идентификатор**.

    b. URL-адрес входа является URL-адресом клиента приложения Atlassian Cloud.   

     ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
12. На портале Azure нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-an-atlassian-cloud-test-user"></a>Создание тестового пользователя Atlassian Cloud

Чтобы пользователи Azure AD могли выполнять вход в Atlassian Cloud, они должны быть подготовлены в Atlassian Cloud.  
В случае Atlassian Cloud подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. В разделе "Site administration" (Администрирование сайта) нажмите кнопку **Users** (Пользователи).

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Нажмите кнопку **Create User** (Создать пользователя), чтобы создать пользователя в приложении Atlassian Cloud.

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Введите **адрес электронной почты**, **имя пользователя** и **полное имя** и предоставьте доступ к приложению. 

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Нажмите кнопку **Create user** (Создать пользователя). После этого пользователю будет отправлено приглашение по электронной почте. Как только пользователь примет приглашение, он станет активным в системе. 

>[!NOTE] 
>Вы также можете создать несколько пользователей, нажав кнопку **Bulk Create** (Массовое создание) в разделе "Users" (Пользователи).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Atlassian Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Atlassian Cloud, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Atlassian Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Atlassian Cloud на панели доступа, вы автоматически войдете в приложение Atlassian Cloud. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png


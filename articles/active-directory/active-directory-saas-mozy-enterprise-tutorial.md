---
title: Руководство по интеграции Azure Active Directory с Mozy Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 37b0cdce9b77b304a24ff3419f618f193fb2ad52
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Учебник. Интеграция Azure Active Directory с Mozy Enterprise

В этом руководстве описано, как интегрировать Mozy Enterprise с Azure Active Directory (Azure AD).

Интеграция Mozy Enterprise с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать, кто будет иметь доступ к Mozy Enterprise.
- Вы можете включить автоматический вход пользователей в Mozy Enterprise (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Mozy Enterprise, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Mozy Enterprise.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Mozy Enterprise из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Добавление Mozy Enterprise из коллекции
Чтобы настроить интеграцию Mozy Enterprise в Azure AD, необходимо добавить Mozy Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mozy Enterprise из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Mozy Enterprise**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. На панели результатов выберите **Mozy Enterprise** и щелкните **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в Mozy Enterprise с использованием тестового пользователя Britta Simon.

Чтобы единый вход в Azure AD работал, необходимо указать, какой пользователь в Mozy Enterprise соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mozy Enterprise.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mozy Enterprise.

Чтобы настроить и проверить единый вход Azure AD в Mozy Enterprise, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Mozy Enterprise](#creating-a-mozy-enterprise-test-user)** требуется для создания пользователя Britta Simon в Mozy Enterprise, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Mozy Enterprise.

**Чтобы настроить единый вход Azure AD в Mozy Enterprise, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Mozy Enterprise** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Mozy Enterprise** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Свяжитесь со [службой поддержки Mozy Enterprise](http://support.mozy.com/), чтобы получить эти значения.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Mozy Enterprise** щелкните **Настроить Mozy Enterprise**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. В другом окне веб-браузера войдите на сайт Mozy Enterprise компании в качестве администратора.

8. В разделе **Конфигурация** щелкните **Политика проверки подлинности**.
   
   ![Политика аутентификации](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "Политика аутентификации")

9. В разделе **Политика проверки подлинности** сделайте следующее:
   
   ![Политика аутентификации](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "Политика аутентификации")
   
   a. Для параметра **Поставщик** выберите значение **Служба каталогов**.
   
   Б. Выберите **Использовать LDAP для отправки**.
   
   c. Щелкните вкладку **Проверка подлинности SAML** .
   
   d. В текстовое поле **URL-адрес входа** вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
   
   д. В текстовое поле **SAML Endpoint** (Конечная точка SAML) вставьте значение **SAML Endpoint** (Идентификатор сущности SAML), скопированное на портале Azure.
   
   f. Откройте скачанный сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **SAML Certificate** (Сертификат SAML).
   
   ж. Установите флажок **Включить единый вход для администраторов для входа с учетными данными сети**.
   
   h. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Создание тестового пользователя Mozy Enterprise

Чтобы дать возможность пользователям Azure AD входить в Mozy Enterprise, необходимо в Mozy Enterprise подготовить для них учетные записи. В случае с Mozy Enterprise подготовка выполняется вручную.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей Mozy Enterprise или API, предоставляемые Mozy Enterprise для подготовки учетных записей пользователей AAD.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход в клиент **Mozy Enterprise** .

2. Щелкните **Users** (Пользователи), а затем — **Add New User** (Добавить нового пользователя).
   
   ![Пользователи](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "Пользователи")
   
   >[!NOTE]
   >Параметр **Add New User** (Добавить нового пользователя) отображается только в том случае, если в качестве поставщика в списке **Authentication policy** (Политика проверки подлинности) выбрать **Mozy**. Если настроена аутентификация SAML, то пользователи добавляются автоматически при первом входе посредством единого входа.
    
3. В диалоговом окне создания нового пользователя выполните следующие действия:
   
   ![Добавление пользователей](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "добавление пользователей")
   
   a. В списке **Выберите группу** выберите группу.
   
   Б. В списке **Тип пользователя** выберите тип.
   
   c. В текстовом поле **Имя пользователя** введите имя пользователя Azure AD.
   
   d. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя Azure AD.
   
   д. Выберите **Отправить пользователю электронное сообщение с указаниями**.
   
   f. Щелкните **Добавить пользователей**.

     >[!NOTE]
     > После создания учетной записи соответствующему пользователю Azure AD будет отправлено электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mozy Enterprise.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Mozy Enterprise, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Mozy Enterprise**.

    ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Когда вы щелкните элемент Mozy Enterprise на панели доступа, появится страница входа в приложения Mozy Enterprise.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png


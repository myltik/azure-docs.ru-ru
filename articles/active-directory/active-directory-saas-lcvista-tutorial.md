---
title: Руководство. Интеграция Azure Active Directory с LCVista | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и LCVista.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 8ab0bb9bce6c3c714878edf1db2819beeaf9f51a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Руководство. Интеграция Azure Active Directory с LCVista

В этом руководстве описано, как интегрировать LCVista с Azure Active Directory (Azure AD).

Интеграция LCVista c Azure AD обеспечивает следующие преимущества.

- В Azure AD вы сможете контролировать, у кого есть доступ к LCVista.
- Вы можете включить автоматический вход пользователей в LCVista (единый вход) с помощью их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LCVista, вам потребуются следующие компоненты:

- подписка Azure AD;
- подписка LCVista с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавления LCVista из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-lcvista-from-the-gallery"></a>Добавления LCVista из коллекции.
Чтобы настроить интеграцию LCVista в Azure AD, необходимо добавить LCVista из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LCVista из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LCVista**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_search.png)

5. На панели результатов выберите **LCVista**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в LCVista с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD, Azure AD необходимо знать, какой пользователь в LCVista соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LCVista.

Чтобы установить эту связь, следует назначить значение **имени пользователя** в Azure AD в качестве значения **имени пользователя** в LCVista.

Чтобы настроить и проверить единый вход Azure AD в LCVista, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LCVista](#creating-a-lcvista-test-user)** требуется для создания в LCVista пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure, а также как настроить его в приложении LCVista.

**Чтобы настроить единый вход Azure AD в LCVista, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **LCVista** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_samlbase.png)

3. В разделе **Домены и URL-адреса приложения LCVista** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.lcvista.com/rainier/login`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса единого входа. Чтобы получить их, обратитесь в [службу поддержки клиентов LCVista](https://lcvista.com/contact). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация LCVista** щелкните **настройку LCVista**, чтобы открыть окно **настройки единого входа**. Скопируйте **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_configure.png) 

7.  Войдите в приложение LCVista как администратор.

8. В разделе **SAML Config** (Конфигурация SAML) установите флажок **Enable SAML login** (Включить вход SAML), а затем введите сведения, представленные на изображении ниже. 

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_config.png)

    a. Вставьте **URL-адрес издателя**, скопированный из Azure AD в раздел **Идентификатор сущности**. 

    Б. Вставьте **URL-адрес службы единого входа**, скопированный из Azure AD в раздел **URL-адрес**.

    c. Из метаданных (XML), которые вы скачали на портале Azure, скопируйте значение **X509Certificate** и вставьте его в раздел **x509 Certificate** (Сертификат x509).

    d. В текстовом поле **First name attribute** (Атрибут имени) вставьте значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    д. В текстовом поле **Last name attribute** (Атрибут фамилии) вставьте значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. В текстовом поле **Email attribute** (Атрибут электронной почты) вставьте значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    ж. В текстовом поле **Username** (Атрибут имени пользователя) вставьте значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    д. Нажмите кнопку **Сохранить** , чтобы сохранить параметры.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-lcvista-test-user"></a>Создание тестового пользователя LCVista

В этом разделе описано, как создать пользователя Britta Simon в LCVista. Вам следует обратиться в [службу поддержки клиентов LCVista](https://lcvista.com/contact), чтобы добавить пользователей в приложение LCVista. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure с помощью предоставления доступа к LCVista.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LCVista, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LCVista**.

    ![Настройка единого входа](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа. Щелкните плитку LCVista на панели доступа. Вы будете перенаправлены на страницу входа организации. После успешного входа вы сможете войти в приложение LCVista. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_203.png


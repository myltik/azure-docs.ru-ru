---
title: Руководство. Интеграция Azure Active Directory с приложениями Splunk Enterprise и Splunk Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Splunk Enterprise и Splunk Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: 8b06680c872f567cef3fd2821e40a814b834c3a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350582"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Руководство. Интеграция Azure Active Directory с приложениями Splunk Enterprise и Splunk Cloud

В этом руководстве описано, как интегрировать Splunk Enterprise и Splunk Cloud с Azure Active Directory (Azure AD).

Интеграция Splunk Enterprise и Splunk Cloud с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Splunk Enterprise и Splunk Cloud.
- Вы можете включить автоматический вход пользователей в Splunk Enterprise и Splunk Cloud (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложениями Splunk Enterprise и Splunk Cloud, вам потребуется следующее:

- подписка Azure AD;
- подписка Splunk Enterprise и Splunk Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Splunk Enterprise и Splunk Cloud из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Добавление Splunk Enterprise и Splunk Cloud из коллекции.
Чтобы настроить интеграцию Splunk Enterprise и Splunk Cloud с Azure AD, необходимо добавить Splunk Enterprise и Splunk Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Splunk Enterprise и Splunk Cloud из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Splunk Enterprise и Splunk Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_search.png)

5. В области результатов выберите **Splunk Enterprise и Splunk Cloud**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описывается настройка и проверка единого входа Azure AD в Splunk Enterprise и Splunk Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Splunk Enterprise и Splunk Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Splunk Enterprise и Splunk Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Splunk Enterprise и Splunk Cloud.

Чтобы настроить и проверить единый вход Azure AD в Splunk Enterprise и Splunk Cloud, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Splunk Enterprise и Splunk Cloud](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)** требуется для создания пользователя Britta Simon в Splunk Enterprise и Splunk Cloud, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Splunk Enterprise и Splunk Cloud.

**Чтобы настроить единый вход Azure AD в Splunk Enterprise и Splunk Cloud, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Splunk Enterprise и Splunk Cloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_samlbase.png)

3. Если требуется настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домен и URL-адреса Splunk Enterprise и Splunk Cloud** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<splunkserverUrl>/en-US/app/launcher/home`
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес сервера Splunk Server.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<splunkserver>/saml/acs`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Splunk Enterprise и Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_400.png)

6. Для настройки единого входа на стороне **Splunk Enterprise и Splunk Cloud** необходимо отправить загруженные **метаданные в формате XML** в [службу поддержки Splunk Enterprise и Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Создание тестового пользователя Splunk Enterprise и Splunk Cloud

В этом разделе описано, как создать пользователя Britta Simon в Splunk Enterprise и Splunk Cloud. Чтобы добавить пользователей на платформу Splunk Enterprise и Splunk Cloud, обратитесь в [службу поддержки Splunk Enterprise и Splunk Cloud](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Splunk Enterprise и Splunk Cloud.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Splunk Enterprise и Splunk Cloud, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Splunk Enterprise и Splunk Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Splunk Enterprise и Splunk Cloud на панели доступа, вы автоматически войдете в приложение Splunk Enterprise и Splunk Cloud.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_203.png


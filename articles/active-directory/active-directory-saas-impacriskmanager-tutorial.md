---
title: Руководство по интеграции Azure Active Directory с IMPAC Risk Manager | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в IMPAC Risk Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: d7d45356e7b84d5a4ecbfc964fc1d4b5d8cc8db4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345376"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Руководство по интеграции Azure Active Directory с IMPAC Risk Manager

В этом руководстве описано, как интегрировать IMPAC Risk Manager с Azure Active Directory (Azure AD).

Интеграция Azure AD с IMPAC Risk Manager обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к IMPAC Risk Manager.
- Вы можете включить автоматический вход пользователей в IMPAC Risk Manager (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением IMPAC Risk Manager, вам потребуется следующее:

- подписка Azure AD;
- подписка IMPAC Risk Manager с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление IMPAC Risk Manager из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Добавление IMPAC Risk Manager из коллекции.
Чтобы настроить интеграцию IMPAC Risk Manager в Azure AD, необходимо добавить IMPAC Risk Manager из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IMPAC Risk Manager из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **IMPAC Risk Manager** и выберите **IMPAC Risk Manager** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![IMPAC Risk Manager в списке результатов](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в IMPAC Risk Manager с использованием тестового пользователя Britta Simon.

Чтобы активировать единый вход, Azure AD необходима информация о том, какой пользователь в IMPAC Risk Manager соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IMPAC Risk Manager.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в IMPAC Risk Manager.

Чтобы настроить и проверить единый вход Azure AD в IMPAC Risk Manager, потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя IMPAC Risk Manager](#create-a-impac-risk-manager-test-user)** требуется для того, чтобы в IMPAC Risk Manager существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении IMPAC Risk Manager.

**Чтобы настроить единый вход Azure AD в IMPAC Risk Manager, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **IMPAC Risk Manager** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе **Домены и URL-адреса приложения IMPAC Risk Manager** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения IMPAC Risk Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. В текстовом поле **Идентификатор** введите значение, предоставленное IMPAC.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | Среда | Шаблон URL-адреса |
    | ---------------|--------------- |    
    | Рабочая среда |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Промежуточное хранение и обучение  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Разработка  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Контроль качества |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Тестирование |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения IMPAC Risk Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | Среда | Шаблон URL-адреса |
    | ---------------|--------------- |    
    | Рабочая среда |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Промежуточное хранение и обучение  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Разработка  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Контроль качества |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Тестирование |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz).

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_400.png)
    
7. В разделе **Настройка IMPAC Risk Manager** щелкните **Настройка IMPAC Risk Manager**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML, идентификатор сущности SAML**, а также **URL-адрес выхода** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

8. Чтобы настроить единый вход на стороне **IMPAC Risk Manager**, нужно отправить скачанный **сертификат в кодировке Base64**, **URL-адрес выхода, идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** в [службу поддержки IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Создание тестового пользователя приложения IMPAC Risk Manager

В этом разделе описано, как создать пользователя Britta Simon в приложении IMPAC Risk Manager. Чтобы добавить пользователей на платформу IMPAC Risk Manager, обратитесь в [службу поддержки IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz). Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к IMPAC Risk Manager, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в IMPAC Risk Manager, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **IMPAC Risk Manager**.

    ![Ссылка на IMPAC Risk Manager в списке приложений](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку IMPAC Risk Manager на панели доступа, вы автоматически войдете в приложение IMPAC Risk Manager.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_203.png


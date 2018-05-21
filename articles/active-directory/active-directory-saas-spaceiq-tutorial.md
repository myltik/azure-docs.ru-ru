---
title: Руководство по интеграции Azure Active Directory с приложением SpaceIQ | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: f76be8db123558d87dfa5c1756f5928aed97d63f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Руководство по интеграции Azure Active Directory со SpaceIQ

В этом руководстве описано, как интегрировать SpaceIQ с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SpaceIQ обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к SpaceIQ.
- Вы можете включить автоматический вход пользователей в SpaceIQ (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением SpaceIQ, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа SpaceIQ.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SpaceIQ из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-spaceiq-from-the-gallery"></a>Добавление SpaceIQ из коллекции
Чтобы настроить интеграцию SpaceIQ с Azure AD, необходимо добавить SpaceIQ из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SpaceIQ из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SpaceIQ**, выберите **SpaceIQ** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SpaceIQ в списке результатов](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в SpaceIQ с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SpaceIQ соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SpaceIQ.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SpaceIQ.

Чтобы настроить и проверить единый вход Azure AD в SpaceIQ, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения SpaceIQ](#create-a-spaceiq-test-user)** требуется для того, чтобы в SpaceIQ существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SpaceIQ.

**Чтобы настроить единый вход Azure AD в SpaceIQ, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **SpaceIQ** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SpaceIQ** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://api.spaceiq.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://api.spaceiq.com/saml/<instanceid>/callback`.

    > [!NOTE] 
    > Замените эти значения фактическими URL-адресом ответа и идентификатором, как описано позже в этом руководстве.
 
4. В разделе **Сертификат подписи SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-spaceiq-tutorial/tutorial_general_400.png)

6. В разделе **Настройка SpaceIQ** щелкните **Настроить SpaceIQ**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Настройка SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Откройте новое окно браузера и войдите в среду SpaceIQ в качестве администратора.

8. После входа щелкните значок головоломки в правом верхнем углу, а затем — **Integrations** (Интеграции).

    ![Параметры учетной записи](./media/active-directory-saas-spaceiq-tutorial/setting1.png) 

9. В разделе **All PROVISIONING & SSO** (Все подготовки и единый вход), щелкните плитку **Azure**, чтобы добавить экземпляр Azure в качестве поставщика удостоверений.

    ![Значок SAML](./media/active-directory-saas-spaceiq-tutorial/setting2.png)

10. В диалоговом окне **единого входа** сделайте следующее.

    ![Параметры проверки подлинности SAML](./media/active-directory-saas-spaceiq-tutorial/setting3.png)

    a. В текстовом поле **SAML Issuer URL** (URL-адрес издателя SAML) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML) из окна настройки приложения Azure AD.
    
    Б. Скопируйте значение **SAML CallBack Endpoint URL (read-only)** (URL-адрес конечной точки обратного вызова SAML (только для чтения)) и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Домены и URL-адреса приложения SpaceIQ**.
    
    c. Скопируйте значение **SAML Audience URI (read-only)** (URI аудитории SAML (только для чтения)) и вставьте его в поле **Идентификатор** на портале Azure в разделе **Домены и URL-адреса приложения SpaceIQ**.

    d. Откройте загруженный сертификат в блокноте, скопируйте его содержимое и вставьте его в текстовое поле **Сертификат X.509**.
    
    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-spaceiq-test-user"></a>Создание тестового пользователя SpaceIQ

В этом разделе описано, как создать пользователя Britta Simon в приложении SpaceIQ. Обратитесь в [службу поддержки SpaceIQ](mailto:eng@spaceiq.com), чтобы добавить пользователей на платформу SpaceIQ. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SpaceIQ.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SpaceIQ, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SpaceIQ**.

    ![Ссылка на SpaceIQ в списке "Приложения"](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SpaceIQ на панели доступа, вы автоматически войдете в приложение SpaceIQ.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_203.png


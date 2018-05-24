---
title: Руководство по интеграции Azure Active Directory с SAML SSO for Bitbucket by resolution GmbH | Документация Майкрософт
description: Узнайте, как настроить единый вход для Azure Active Directory и SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: e1eeed7f6597cf015482e831031d9611646db215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Руководство по интеграции Azure Active Directory с SAML SSO for Bitbucket by resolution GmbH

В этом руководстве описано, как интегрировать SAML SSO for Bitbucket by resolution GmbH с Azure Active Directory (Azure AD).

Интеграция SAML SSO for Bitbucket by resolution GmbH с Azure AD предоставляет следующие преимущества:

- Можно управлять доступом пользователей Azure AD к SAML SSO for Bitbucket by resolution GmbH.
- Можно включить автоматический вход пользователей в SAML SSO for Bitbucket by resolution GmbH (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Для настройки интеграции Azure AD с SAML SSO for Bitbucket by resolution GmbH требуется:

- подписка Azure AD;
- подписка SAML SSO for Bitbucket by resolution GmbH с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAML SSO for Bitbucket by resolution GmbH из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Добавление SAML SSO for Bitbucket by resolution GmbH из коллекции
Чтобы настроить интеграцию SAML SSO for Bitbucket by resolution GmbH в Azure AD, необходимо добавить SAML SSO for Bitbucket by resolution GmbH из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAML SSO for Bitbucket by resolution GmbH из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. Чтобы добавить приложение, в поле поиска введите **SAML SSO for Bitbucket by resolution GmbH**, затем на панели результатов выберите **SAML SSO for Bitbucket by resolution GmbH** и нажмите кнопку **Добавить**.

    ![Список результатов SAML SSO for Bitbucket by resolution GmbH](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAML SSO for Bitbucket by resolution GmbH с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAML SSO for Bitbucket by resolution GmbH соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAML SSO for Bitbucket by resolution GmbH.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAML SSO for Bitbucket by resolution GmbH.

Чтобы настроить и проверить единый вход Azure AD в SAML SSO for Bitbucket by resolution GmbH, требуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAML SSO for Bitbucket by resolution GmbH](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** требуется, чтобы создать в SAML SSO for Bitbucket by resolution GmbH пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении SAML SSO for Bitbucket by resolution GmbH.

**Чтобы настроить единый вход Azure AD в SAML SSO for Bitbucket by resolution GmbH, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **SAML SSO for Bitbucket by resolution GmbH** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном IDP, то в разделе **Домены и URL-адреса приложения SAML SSO for Bitbucket by resolution GmbH** сделайте следующее:

    ![Информация о едином входе доменов и URL-адресов приложения SAML SSO for Bitbucket by resolution GmbH](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Информация о едином входе доменов и URL-адресов приложения SAML SSO for Bitbucket by resolution GmbH](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Для получения этих значений обратитесь к [группе поддержки SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-bitbucket-tutorial/tutorial_general_400.png)
    
7. Войдите на свой корпоративный сайт SAML SSO for Bitbucket by resolution GmbH с правами администратора.

8. Справа от главной панели инструментов щелкните **Параметры**.

9. В разделе "Учетные записи" в строке меню щелкните **SAML SingleSignOn**.

    ![Samlsingle](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

10. На **странице конфигурации подключаемого модуля SAML SIngleSignOn** щелкните **Add idp** (Добавить поставщик удостоверений). 

    ![Добавление поставщика удостоверений](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_addidp.png)

11. На странице **Choose your SAML Identity Provider** (Выбор поставщика удостоверений SAML) выполните следующие действия:

    ![Поставщик удостоверений](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Выберите **Azure AD** в качестве **типа поставщика удостоверений**.

    Б. В текстовом поле **Имя** введите имя.

    c. В текстовом поле **Описание** введите описание.

    d. Нажмите кнопку **Далее**.

12. На странице **Identity provider configuration page** (Настройка поставщика удостоверений) нажмите кнопку **Далее**.

    ![Настройка удостоверения](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

13.  На странице **импорта метаданных SAML поставщика удостоверений** щелкните **Загрузить файл**, чтобы передать файл **метаданных в формате XML**, скачанный на портале Azure.

    ![idpmetadata](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
14. Нажмите кнопку **Далее**.

15. Нажмите кнопку **Сохранить параметры**.

    ![Сохранение](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-bitbucket-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-bitbucket-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-bitbucket-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-bitbucket-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Создание тестового пользователя в SAML SSO for Bitbucket by resolution GmbH

Цель этого раздела — создать в приложении SAML SSO for Bitbucket by resolution GmbH пользователя с именем Britta Simon. SAML SSO for Bitbucket by resolution GmbH обеспечивает своевременную подготовку, а также создание пользователей вручную. Для согласования ваших требований обратитесь в [службу поддержки клиентов SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления ей доступа к SAML SSO for Bitbucket by resolution GmbH.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAML SSO for Bitbucket by resolution GmbH, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **SAML SSO for Bitbucket by resolution GmbH**.

    ![Ссылка на SAML SSO for Bitbucket by resolution GmbH link в списке приложений](./media/active-directory-saas-bitbucket-tutorial/tutorial_bitbucket_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAML SSO for Bitbucket by resolution GmbH на панели доступа, вы сможете автоматически войти в свое приложение SAML SSO for Bitbucket by resolution GmbH.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bitbucket-tutorial/tutorial_general_203.png


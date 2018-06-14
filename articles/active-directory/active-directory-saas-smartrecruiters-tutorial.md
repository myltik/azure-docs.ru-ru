---
title: Руководство по интеграции Azure Active Directory с SmartRecruiters | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.openlocfilehash: 3c455b6dee2f935ed4551b0a4f905688fc192168
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352401"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Руководство по интеграции Azure Active Directory с SmartRecruiters

Из этого руководства вы узнаете, как интегрировать SmartRecruiters с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SmartRecruiters обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к SmartRecruiters.
- Вы можете включить автоматический вход пользователей в SmartRecruiters (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SmartRecruiters, вам потребуется следующее:

- подписка Azure AD;
- Подписка SmartRecruiters с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SmartRecruiters из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-smartrecruiters-from-the-gallery"></a>Добавление SmartRecruiters из коллекции
Чтобы настроить интеграцию SmartRecruiters с Azure AD, необходимо добавить SmartRecruiters из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SmartRecruiters из коллекции, выполните инструкции ниже.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SmartRecruiters**, выберите **SmartRecruiters** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SmartRecruiters в списке результатов](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SmartRecruiters при помощи тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в SmartRecruiters соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SmartRecruiters.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SmartRecruiters.

Чтобы настроить и проверить единый вход Azure AD в SmartRecruiters, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создав тестового пользователя SmartRecruiters](#create-a-smartrecruiters-test-user)** Britta Simon, мы сможем связать его с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Из этого раздела вы узнаете, как включить единый вход Azure AD на портале Azure и настроить его в приложении SmartRecruiters.

**Чтобы настроить единый вход Azure AD в SmartRecruiters, выполните инструкции ниже.**

1. На портале Azure на странице интеграции с приложением **SmartRecruiters** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

3. Чтобы настроить приложение в режиме, инициированном **IDP**, в разделе **SmartRecruiters Domain and URLs** (Домен и URL-адреса приложения SmartRecruiters) сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/). 

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните сертификат на локальном компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_400.png)
    
7. В разделе **SmartRecruiters Configuration** (Конфигурация SmartRecruiters) щелкните **Configure SmartRecruiters** (Настроить SmartRecruiters), чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

8. В другом окне веб-браузера войдите на корпоративный сайт SmartRecruiters в качестве администратора.

9. Последовательно выберите элементы **Параметры / Администратор**.

    ![Настройка SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/configure.png)

10. В разделе **Конфигурация** щелкните **Web SSO** (Единый вход через Интернет).

    ![Настройка SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/configure1.png)

11. Переключите **Enable Web SSO** (Включить единый вход через Интернет).

    ![Настройка SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/configure2.png)

12. В разделе **Identity Provider Configuration** (Конфигурация поставщика удостоверений) выполните следующие действия:

    ![Настройка SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/configure4.png)

    a. В текстовое поле **Identity Provider URL** (URL-адрес поставщика удостоверений) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    Б. Откройте **сертификат (Base64)**, который вы скачали с портала Azure, и вставьте его содержимое в текстовое поле **Identity Provider certificate** (Сертификат поставщика удостоверений).

13. Щелкните **Save Web SSO configuration** (Сохранить конфигурацию единого входа через Интернет).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-smartrecruiters-test-user"></a>Создание тестового пользователя SmartRecruiters

Из этого раздела вы узнаете, как создать пользователя Britta Simon в SmartRecruiters. Для добавления пользователей на платформе SmartRecruiters обратитесь в [службу поддержки SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/). Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе пользователю Britta Simon предоставляется разрешение на использование единого входа Azure для доступа к SmartRecruiters.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SmartRecruiters, выполните инструкции ниже.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SmartRecruiters**.

    ![Ссылка на SmartRecruiters в списке "Приложения"](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SmartRecruiters на панели доступа, вы автоматически войдете в приложение SmartRecruiters.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_203.png


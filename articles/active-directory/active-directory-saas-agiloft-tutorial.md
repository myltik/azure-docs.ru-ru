---
title: Руководство по интеграции Azure Active Directory с Agiloft | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Agiloft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: aca13814-cdbd-46b8-93dc-1578099c5ee4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: 296b972eced0bfb54435489d727270cc22c75798
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft"></a>Руководство по интеграции Azure Active Directory с Agiloft

В этом руководстве описано, как интегрировать Agiloft с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Agiloft обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Agiloft.
- Вы можете включить автоматический вход пользователей в Agiloft (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Agiloft, вам потребуется:

- подписка Azure AD;
- подписка Agiloft с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Agiloft из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-agiloft-from-the-gallery"></a>Добавление Agiloft из коллекции
Чтобы настроить интеграцию Agiloft с Azure AD, необходимо добавить Agiloft из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Agiloft из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Agiloft**, выберите **Agiloft** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Agiloft в списке результатов](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Agiloft с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Agiloft соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Agiloft.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Agiloft.

Чтобы настроить и проверить единый вход Azure AD в Agiloft, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Agiloft](#create-an-agiloft-test-user)** нужно для того, чтобы в Agiloft существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Agiloft.

**Чтобы настроить единый вход Azure AD в Agiloft, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Agiloft** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе**Домены и URL-адреса приложения Agiloft** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Agiloft](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/project/<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/project/<KB_NAME>` |

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com:443/gui2/spsamlsso?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>` |

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Agiloft](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Agiloft](https://www.agiloft.com/support-login.htm). 

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-agiloft-tutorial/tutorial_general_400.png)
    
7. В разделе **Конфигурация Agiloft** щелкните **Настроить Agiloft**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Agiloft](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_configure.png) 

8. В другом окне веб-браузера войдите на корпоративный веб-сайт Agiloft в качестве администратора.

9. В области слева щелкните **Setup** (Настройка), а затем **Access** (Доступ).

    ![Настройка Agiloft](./media/active-directory-saas-agiloft-tutorial/setup1.png) 

10. Нажмите кнопку **Configure SAML 2.0 Single Sign-On** (Настройка единого входа SAML 2.0). 
    
    ![Настройка Agiloft](./media/active-directory-saas-agiloft-tutorial/setup2.png) 

11. Откроется диалоговое окно мастера настройки. В диалоговом окне перейдите на вкладку **Identity Provider Details** (Сведения о поставщике удостоверений) и заполните следующие поля:  
    
    ![Настройка Agiloft](./media/active-directory-saas-agiloft-tutorial/setup4.png) 

    a. В текстовое поле **IdP Entity Id / Issuer** (Идентификатор сущности поставщика удостоверений / издатель) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    Б. В текстовое поле **IdP Login URL** (URL-адрес входа поставщика удостоверений) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. В текстовое поле **IdP Logout URL** (URL-адрес выхода поставщика удостоверений) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    d. Откройте в блокноте **сертификат в кодировке Base-64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **IdP Provided X.509 certificate contents** (Содержимое предоставленного поставщиком удостоверений сертификата X.509).

    д. Нажмите кнопку **Готово**


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-agiloft-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-agiloft-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-agiloft-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-agiloft-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-agiloft-test-user"></a>Создание тестового пользователя в Agiloft

Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. В этом разделе никакие действия с вашей стороны не требуются.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Agiloft.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Agiloft, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Agiloft**.

    ![Ссылка на Agiloft в списке "Приложения"](./media/active-directory-saas-agiloft-tutorial/tutorial_agiloft_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Agiloft на панели доступа, вы автоматически войдете в приложение Agiloft.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-agiloft-tutorial/tutorial_general_203.png


---
title: Руководство. Интеграция Azure Active Directory с Workstars | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 090dc4dfe5348f5af84999bfbeab242747a7d0ef
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Учебник. Интеграция Azure Active Directory с Workstars

В этом учебнике описано, как интегрировать приложение Workstars с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Workstars обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Workstars;
- вы можете включить автоматический вход пользователей в Workstars (единый вход) под учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Workstars, вам потребуется:

- подписка Azure AD;
- подписка Workstars с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Workstars из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-workstars-from-the-gallery"></a>Добавление Workstars из коллекции
Чтобы настроить интеграцию Workstars с Azure AD, необходимо добавить Workstars из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workstars из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Workstars**, выберите **Workstars** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Workstars в списке результатов](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workstars с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Workstars соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workstars.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workstars.

Чтобы настроить и проверить единый вход Azure AD в Workstars, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workstars](#create-a-workstars-test-user)** требуется для того, чтобы в Workstars существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Workstars.

**Чтобы настроить единый вход Azure AD в Workstars, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Workstars** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workstars** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Workstars](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://workstars.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.workstars.com/saml/login_check`.

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь к [группе поддержки Workstars](https://support.workstars.com).
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-workstars-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Workstars** щелкните **Настроить Workstars**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Workstars](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_configure.png) 

7. В другом окне браузера войдите на корпоративный сайт Workstars с правами администратора.

8. На главной панели инструментов щелкните **Settings**(Настройки).

    ![Настройки Workstars](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_sett.png)

9. Откройте элементы **Sign On** (Вход)  > **Settings** (Настройки).

    ![Workstars, раздел "Вход"](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_signon.png)

    ![Workstars, раздел "Настройки"](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_settings.png)

10. На странице **Single Sign On (SAML) - Settings** (Параметры единого входа (SAML)) выполните следующие действия.
    
    ![Настройки SAML в Workstars](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_saml.png)

    a. В текстовом поле **Identity Provider Name** (Имя поставщика удостоверений) введите **Office 365**.

    Б. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c. Скопируйте в блокнот содержимое скачанного сертификата, а затем вставьте его в текстовое поле **x509 Certificate** (Сертификат X.509). 

    d. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.
    
    д. В текстовое поле **Remote Logout URL** (URL-адрес удаленного выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure. 

    f. Для параметра **Name ID** (Идентификатор имени) выберите значение **Email (Default)** (Адрес электронной почты (по умолчанию)).

    ж. Щелкните **Confirm** (Подтвердить).
    
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-workstars-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-workstars-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-workstars-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-workstars-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-workstars-test-user"></a>Создание тестового пользователя Workstars

В этом разделе описано, как создать пользователя Britta Simon в приложении Workstars. Обратитесь в [службу поддержки Workstars](https://support.workstars.com), чтобы добавить пользователей на платформу Workstars.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Workstars.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Workstars, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Workstars**.

    ![Ссылка на Workstars в списке "Приложения"](./media/active-directory-saas-workstars-tutorial/tutorial_workstars_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workstars на панели доступа, вы автоматически войдете в приложение Workstars.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workstars-tutorial/tutorial_general_203.png


---
title: Руководство. Интеграция Azure Active Directory с Central Desktop | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Central Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 92c24688cf3d9baefcedcf22c915752b2d29b53c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Учебник. Интеграция Azure Active Directory с Central Desktop

В этом руководстве описано, как интегрировать Central Desktop с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Central Desktop обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Central Desktop.
- Вы можете включить автоматический вход пользователей в Central Desktop с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Central Desktop, вам потребуется:

- подписка Azure AD;
- подписка на Central Desktop с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас еще нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление Central Desktop из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="add-central-desktop-from-the-gallery"></a>Добавление Central Desktop из коллекции
Чтобы настроить интеграцию Central Desktop с Azure AD, необходимо добавить Central Desktop из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Central Desktop из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новые приложения, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Central Desktop**. Чтобы добавить приложение, на панели результатов выберите **Central Desktop** и нажмите кнопку **Добавить**.

    ![Central Desktop в списке результатов](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Central Desktop с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой из пользователей в Central Desktop соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Central Desktop.

Значение **имени пользователя**  в Central Desktop должно соответствовать **имени пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями.

Чтобы настроить и проверить единый вход Azure AD в Central Desktop, вам потребуется выполнить действия в следующих стандартных блоках:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя приложения Central Desktop](#create-a-central-desktop-test-user) требуется для того, чтобы в Central Desktop существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Central Desktop.

**Чтобы настроить единый вход Azure AD в Central Desktop, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Central Desktop** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** из раскрывающегося списка **Режим** выберите пункт **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Central Desktop** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.centraldesktop.com`.

    Б. В поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить их, обратитесь в [службу поддержки клиентов Central Desktop](https://imeetcentral.com/contact-us). 

4. В разделе **Сертификат подписи SAML** выберите **Сертификат**. Затем сохраните файл сертификата на своем компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Нажмите кнопку **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. В разделе **Настройка Central Desktop** выберите **Настроить Central Desktop**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка приложения Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Войдите в клиент **Central Desktop**.

8. Перейдите в меню **Параметры**. Выберите **Advanced** (Дополнительно), а затем **Single Sign On** (Единый вход).

    ![Настройка — Дополнительно](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Настройка — Дополнительно")

9. На странице **Single Sign On Settings** (Параметры единого входа) сделайте следующее:

    ![Параметры единого входа](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Параметры единого входа")
    
    a. Установите флажок **Разрешить единый вход SAML версии 2**.
    
    Б. В поле **SSO URL** (URL-адрес единого входа) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.
    
    c. В поле **SSO Login URL** (URL-адрес единого входа) вставьте значение **URL-адреса единого входа SAML**, скопированное на портале Azure.
    
    d. В поле **SSO Logout URL** (URL-адрес единого выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

10. В разделе **Message Signature Verification Method** (Метод проверки подписей в сообщениях) сделайте следующее:

    ![Метод проверки подписей в сообщениях](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "Метод проверки подписей в сообщениях"). А. Выберите **Сертификат**.
    
    Б. В списке **SSO Certificate** (Сертификат единого входа) выберите значение **RSH SHA256**.
    
    c. Откройте скачанный сертификат в Блокноте. Затем скопируйте содержимое сертификата и вставьте его в поле **SSO Certificate** (Сертификат единого входа).
        
    d. Установите флажок **Отображать ссылку на страницу входа SAML версии 2**.
    
    д. Нажмите кнопку **Обновить**.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**, а затем откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-central-desktop-test-user"></a>Создание тестового пользователя Central Desktop

Чтобы пользователи Azure AD могли входить систему, их необходимо подготовить в Central Desktop. В этом разделе описывается порядок создания учетных записей пользователей Azure AD в Central Desktop.

> [!NOTE]
> Для подготовки учетных записей пользователя Azure AD вы можете использовать любые другие средства создания учетной записи пользователя Central Desktop или API, предоставляемые Central Desktop.

**Чтобы подготовить учетные записи пользователей для Central Desktop, сделайте следующее:**

1. Войдите в клиент Central Desktop.

2. Выберите **People** (Люди) > **Internal Members** (Внутренние участники).

3. Выберите **Add Internal Members** (Добавить внутренних участников).

    ![Люди](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Люди")
    
4. В поле **Email Address of New Members** (Адреса электронной почты новых участников) введите учетную запись Azure AD, которую вы хотите подготовить, а затем нажмите кнопку **Далее**.

    ![Адреса электронной почты новых участников](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "Адреса электронной почты новых участников")

5. Выберите **Add Internal members** (Добавить внутренних участников).

    ![Добавление внутренних участников](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Добавление внутренних участников")
   
   >[!NOTE]
   >Пользователи, которые были добавлены, получат сообщение электронной почты, содержащее ссылку для активации учетной записи.
   
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Central Desktop.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Central Desktop, выполните следующие действия:**

1. На портале Azure откройте представление "Приложения". В представлении каталога выберите пункт **Корпоративные приложения**.

2. Выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Central Desktop**.

    ![Ссылка на Central Desktop в списке приложений](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Central Desktop на панели доступа, вы автоматически войдете в приложение Central Desktop.
Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png


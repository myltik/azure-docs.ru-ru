---
title: Руководство по интеграции Azure Active Directory с Adobe Experience Manager | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: d63ee16df9b49517c93cdc405cd2525f578d2a7f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Руководство. Интеграция Azure Active Directory с Adobe Experience Manager

В этом руководстве описано, как интегрировать Adobe Experience Manager с Azure Active Directory (Azure AD).

Интеграция Azure AD с Adobe Experience Manager обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Adobe Experience Manager.
- Вы можете включить автоматический вход пользователей в Adobe Experience Manager с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Adobe Experience Manager, вам потребуется следующее:

- подписка Azure AD;
- подписка Adobe Experience Manager с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление Adobe Experience Manager из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Добавление Adobe Experience Manager из коллекции
Чтобы настроить интеграцию Adobe Experience Manager в Azure AD, необходимо добавить Adobe Experience Manager из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Experience Manager из коллекции, выполните следующие действия.**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Adobe Experience Manager**. На панели результатов выберите **Adobe Experience Manager** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Adobe Experience Manager в списке результатов](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Adobe Experience Manager с использованием тестового пользователя Britta Simon.

Чтобы активировать единый вход, Azure AD необходима информация о том, какой пользователь в Adobe Experience Manager соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Experience Manager.

Назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Adobe Experience Manager. Это действие устанавливает связь между двумя пользователями. 

Чтобы настроить и проверить единый вход Azure AD в Adobe Experience Manager, выполните действия в следующих стандартных блоках.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) требуется для того, чтобы в Adobe Experience Manager существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) позволяет убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Adobe Experience Manager.

**Чтобы настроить единый вход Azure AD в Adobe Experience Manager, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Adobe Experience Manager** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** в меню **Режим** выберите **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Если вы хотите настроить приложение в режиме **поставщика удостоверений**, в разделе**Домены и URL-адреса приложения Adobe Experience Manager** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. В текстовом поле **Идентификатор** введите уникальное значение, которое определяется на сервере AEM. 

    Б. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора и URL-адреса ответа. Для получения этих значений обратитесь к [группе поддержки Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Установите флажок **Показать дополнительные параметры URL-адресов**, Если вы хотите настроить приложение в режиме, инициируемом **поставщиком услуг**, выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    В поле **URL-адрес для входа** введите URL-адрес сервера Adobe Experience Manager. 

5. В разделе **Сертификат подписи SAML** выберите **Сертификат (Base64)**. Затем сохраните файл сертификата на своем компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. В разделе "Конфигурация Adobe Experience Manager" щелкните **Настройка Adobe Experience Manager**, чтобы открыть окно "Настройка единого входа". Скопируйте **URL-адрес службы единого входа SAML**, **идентификатор сущности SAML** и **идентификатор для выхода** из раздела "Краткий справочник".

    ![Ссылка на раздел конфигурации](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Откройте портал администрирования **Adobe Experience Manager** в другом окне браузера.

9. Выберите **Параметры** > **Безопасность** > **Пользователи**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Выберите **Администратора** или любого другого соответствующего пользователя.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Выберите **Account settings** (Параметры учетной записи)  >  **Manage TrustStore** (Управление TrustStore).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Щелкните **Select Certificate File** (Выбрать файл сертификата) в разделе **Add Certificate from CER file** (Добавление сертификата из CER-файла). Найдите и укажите путь к файлу сертификата, скачанному с портала Azure.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Сертификат добавляется в TrustStore. Обратите внимание на псевдоним сертификата.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. На странице **Users** (Пользователи) выберите **authentication-service**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Выберите **Параметры учетной записи** > **Create/Manage KeyStore** (Создание хранилища ключей/Управление хранилищем ключей). Создайте хранилище ключей, указав пароль.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Вернитесь на страницу администрирования. Выберите **Settings** (Параметры)  >  **Operations** (Операции)  >  **Web Console** (Веб-консоль).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Откроется страница конфигурации.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Найдите **Adobe Granite SAML 2.0 Authentication Handler**. Щелкните значок **Add** (Добавить).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Выполните следующие действия на этой странице.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. В текстовом поле **Path** (Путь) введите **/**.

    Б. В поле **IDP URL** (URL-адрес IdP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. В поле **IDP Certificate Alias** (Псевдоним сертификата IdP) введите значение **Certificate Alias** (Псевдоним сертификата), которое вы добавили в TrustStore.

    d. В поле **Security Provided Entity ID** (Идентификатор сущности системы безопасности) введите уникальное значение **SAML Entity ID** (Идентификатор сущности SAML), настроенное на портале Azure.

    д. В поле **Assertion Consumer Service URL** (URL-адрес службы обработчика утверждений) введите значение **URL-адрес ответа**, настроенное на портале Azure.

    f. В поле **Password of Key Store** (Пароль хранилища ключей) введите **пароль**, установленный в хранилище ключей.

    ж. В поле **User Attribute ID** (Идентификатор пользовательского атрибута) введите **идентификатор имени** или другой пользовательский идентификатор, который используется в вашем случае.

    h. Выберите **Autocreate CRX Users** (Автоматическое создание пользователей CRX).

    i. В поле **Logout URL** (URL-адрес выхода) вставьте значение **Sign-Out URL** (URL-адрес выхода), скопированное на портале Azure.

    j. Щелкните **Сохранить**.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**. Откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** нажмите кнопку **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль**. Запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Создание тестового пользователя Adobe Experience Manager

В этом разделе описано, как создать пользователя Britta Simon в приложении Adobe Experience Manager. Если выбран параметр **Autocreate CRX Users** (Автоматическое создание пользователей CRX), то пользователи будут создаваться автоматически после успешной аутентификации. 

Если вы хотите создать пользователей вручную, обратитесь к [группе поддержки Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) для добавления пользователей на платформу Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Adobe Experience Manager.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Adobe Experience Manager, выполните следующие действия.**

1. На портале Azure откройте представление "Приложения". Перейдите к представлению каталога, выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Adobe Experience Manager**.

    ![Ссылка на Adobe Experience Manager в списке приложений](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Adobe Experience Manager" на панели доступа, вы автоматически войдете в приложение Adobe Experience Manager.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png


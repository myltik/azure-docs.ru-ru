---
title: Руководство по интеграции Azure Active Directory с Evernote | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 6926472cad5e124ec3e8636df057044b792827c6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Руководство по интеграции Azure Active Directory с Evernote

В этом руководстве описано, как интегрировать Evernote с Azure Active Directory (Azure AD).

Интеграция Evernote с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Evernote.
- Вы можете включить автоматический вход пользователей в Evernote (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Evernote, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Evernote.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Evernote из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-evernote-from-the-gallery"></a>Добавление Evernote из коллекции
Чтобы настроить интеграцию Evernote с Azure AD, необходимо добавить Evernote из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Evernote из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Evernote**, выберите **Evernote** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Evernote в списке результатов](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Evernote с использованием тестового пользователя Britta Simon.

Для настройки единого входа в Azure AD необходимо знать, какой пользователь в Evernote соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Evernote.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Evernote.

Чтобы настроить и проверить единый вход Azure AD в Evernote, необходимо выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Evernote](#create-an-evernote-test-user)** требуется для создания в Evernote пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении Evernote.

**Чтобы настроить единый вход Azure AD в Evernote, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Evernote** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе **Домены и URL-адреса приложения Evernote** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес `https://www.evernote.com/saml2`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://www.evernote.com/Login.action`   

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

7. В разделе **Настройка Evernote** щелкните **Настроить Evernote**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

8. В другом окне веб-браузера войдите на сайт компании Evernote в качестве администратора.

9. Перейдите в **консоль администрирования**.

    ![Консоль администрирования](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

10. Из **консоли администрирования** перейдите в раздел **Безопасность** и выберите **Единый вход**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

11. Задайте следующие значения.

    ![Настройка сертификата](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Включить единый вход**. Единый вход включен по умолчанию (щелкните **Отключить единый вход**, чтобы удалить требование единого входа).

    Б. Вставьте **URL-адрес службы единого входа SAML**, скопированный на портале Azure, в текстовое поле **SAML HTTP Request URL** (URL-адрес HTTP-запроса SAML).

    c. Откройте в Блокноте скачанный из Azure AD сертификат и скопируйте его содержимое, включая строки BEGIN CERTIFICATE и END CERTIFICATE, в текстовое поле **X.509 Certificate** (Сертификат X.509). 

    Щелкните **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-evernote-test-user"></a>Создание тестового пользователя Evernote

Чтобы пользователи Azure AD могли выполнять вход в Evernote, они должны быть подготовлены для Evernote.  
В случае с Evernote подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите на сайт компании Evernote в качестве администратора.

2. Щелкните **Консоль администрирования**.

    ![Консоль администрирования](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. В **консоли администрирования** выберите **Добавить пользователей**.

    ![Добавление тестового пользователя](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Добавьте адреса электронной почты участников команды** в **соответствующих полях** и нажмите кнопку **Пригласить**.

    ![Добавление тестового пользователя](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. После этого владелец учетной записи Azure Active Directory получает сообщение электронной почты, чтобы принять приглашение.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход в Azure путем предоставления этому пользователю доступа к Evernote.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Evernote, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Evernote**.

    ![Ссылка на Evernote в списке "Приложения"](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Evernote на панели доступа, вы автоматически войдете в приложение. Будет выполнен вход с использованием учетной записи организации, но затем нужно будет войти с помощью личной учетной записи. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png


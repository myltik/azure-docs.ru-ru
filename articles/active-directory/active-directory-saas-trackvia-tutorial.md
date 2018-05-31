---
title: Руководство по интеграции Azure Active Directory с TrackVia | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TrackVia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7010023-bdda-4a19-a335-19904e75b813
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeedes
ms.openlocfilehash: f0b0a2f29a1c14477fcd52e03fdfc47fee728c78
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348399"
---
# <a name="tutorial-azure-active-directory-integration-with-trackvia"></a>Руководство. Интеграция Azure Active Directory с TrackVia

Из этого руководства вы узнаете, как интегрировать приложение TrackVia с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением TrackVia обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к TrackVia.
- Вы можете включить автоматический вход пользователей в TrackVia (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с TrackVia, вам потребуется следующее:

- подписка Azure AD;
- подписка TrackVia с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TrackVia из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-trackvia-from-the-gallery"></a>Добавление TrackVia из коллекции
Чтобы настроить интеграцию TrackVia с Azure AD, необходимо добавить TrackVia из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TrackVia из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **TrackVia**, затем выберите **TrackVia** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![TrackVia в списке результатов](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в TrackVia с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в TrackVia соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TrackVia.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TrackVia.

Чтобы настроить и проверить единый вход Azure AD в TrackVia, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TrackVia](#create-a-trackvia-test-user)** требуется для того, чтобы в TrackVia существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TrackVia.

**Чтобы настроить единый вход Azure AD в TrackVia, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **TrackVia** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_samlbase.png)

3. Чтобы настроить приложение в **режиме, инициированном поставщиком удостоверений**, в разделе**TrackVia Domain and URLs** (Домен и URL-адреса приложения TrackVia) выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_url.png)

    В текстовом поле **Идентификатор** введите значение `TrackVia`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://companyname.trackvia.com`
     
    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Для получения этого значения обратитесь в [службу поддержки клиентов TrackVia](mailto:support@trackvia.com).

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-trackvia-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация TrackVia** щелкните **Настроить TrackVia**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Конфигурация TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_configure.png)
    
8. В другом окне браузера войдите на корпоративный веб-сайт TrackVia с правами администратора.

9. Щелкните параметры **My Account** (Моя учетная запись) TrackVia, выберите вкладку **Single Sign On** (Единый вход) и выполните следующие действия.

    ![Конфигурация TrackVia](./media/active-directory-saas-trackvia-tutorial/configure1.png)

    a. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    Б. Затем нажмите кнопку **Choose File** (Выбор файла), чтобы передать файл метаданных, скачанный с портала Azure.

    c. Нажмите кнопку **Сохранить**

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-trackvia-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-trackvia-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-trackvia-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-trackvia-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-trackvia-test-user"></a>Создание тестового пользователя TrackVia

Цель этого раздела — создать в TrackVia пользователя с именем Britta Simon. Приложение TrackVia поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению TrackVia учетная запись пользователя создается автоматически, если она еще не создана.
>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки TrackVia](mailto:support@trackvia.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Из этого раздела вы узнаете, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TrackVia.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в TrackVia, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **TrackVia**.

    ![Ссылка на TrackVia в списке "Приложения"](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку TrackVia на панели доступа, вы автоматически войдете в приложение TrackVia.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_203.png

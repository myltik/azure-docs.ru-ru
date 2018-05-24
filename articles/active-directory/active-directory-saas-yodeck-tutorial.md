---
title: Руководство по интеграции Azure Active Directory с Yodeck | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: ba48c5d14775004e22dd67a2932c8969e6dfdd6d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354186"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Учебник. Интеграция Azure Active Directory с Yodeck

В этом руководстве описано, как интегрировать Yodeck с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Yodeck обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Yodeck.
- Вы можете включить автоматический вход пользователей в Yodeck (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Yodeck, вам потребуется:

- подписка Azure AD;
- подписка Yodeck с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Yodeck из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-yodeck-from-the-gallery"></a>Добавление Yodeck из коллекции
Чтобы настроить интеграцию Yodeck с Azure AD, необходимо добавить Yodeck из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Yodeck из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Yodeck**, выберите **Yodeck** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Yodeck в списке результатов](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Yodeck с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Yodeck соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Yodeck.

Чтобы настроить и проверить единый вход Azure AD в Yodeck, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Yodeck](#create-a-yodeck-test-user)** требуется для того, чтобы в Yodeck существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Yodeck.

**Чтобы настроить единый вход Azure AD в Yodeck, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Yodeck** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе**Домены и URL-адреса приложения Yodeck** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Yodeck](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    В текстовом поле **Идентификатор (идентификатор сущности)** введите URL-адрес: `https://app.yodeck.com/api/v1/account/metadata/`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Yodeck](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://app.yodeck.com/login`

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. В другом окне веб-браузера войдите на свой корпоративный сайт Yodeck в качестве администратора.

8. Щелкните форму **Параметры пользователя** в правом верхнем углу страницы и выберите **Параметры учетной записи**.

    ![Настройка Yodeck](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Выберите **SAML** и выполните следующие действия.

    ![Настройка Yodeck](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Выберите **Import from URL** (Импорт из URL-адреса).

    Б. В текстовое поле **URL-адрес** вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure, а затем нажмите кнопку **Импорт**.
    
    c. После импорта **URL-адреса метаданных федерации приложения** оставшиеся поля заполнятся автоматически.

    d. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-yodeck-test-user"></a>Создание тестового пользователя Yodeck

Чтобы пользователи Azure AD могли выполнять вход в Yodeck, их следует подготовить в Yodeck.
В случае с Yodeck подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Yodeck в качестве администратора.

2. Щелкните **Параметры пользователя** в правом верхнем углу страницы и выберите **Пользователи**.

    ![Добавление сотрудника](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Щелкните **+User** (Добавить пользователя), чтобы открыть вкладку **Сведения о пользователе**.

    ![Добавление сотрудника](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. На странице диалогового окна **Сведения о пользователе** выполните следующие действия.

    ![Добавление сотрудника](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    Б. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    d. Выберите соответствующий параметр **Разрешения учетной записи** согласно требованиям вашей организации.
    
    д. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Yodeck.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Yodeck, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Yodeck**.

    ![Ссылка на Yodeck в списке приложений](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Yodeck на панели доступа, вы автоматически войдете в приложение Yodeck.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png


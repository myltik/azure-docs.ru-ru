---
title: Руководство. Интеграция Azure Active Directory с OrgChart Now | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 8af344f6aa3cd666d655405f927542850b770ae5
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Руководство. Интеграция Azure Active Directory с OrgChart Now

В этом руководстве описано, как интегрировать OrgChart Now с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением OrgChart Now обеспечивает следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к OrgChart Now.
- Вы можете включить автоматический вход пользователей в OrgChart Now (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с OrgChart Now, вам потребуется:

- подписка Azure AD;
- подписка OrgChart Now с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление OrgChart Now из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-orgchart-now-from-the-gallery"></a>Добавление OrgChart Now из коллекции.
Чтобы настроить интеграцию OrgChart Now с Azure AD, необходимо добавить OrgChart Now из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OrgChart Now из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **OrgChart Now**, выберите **OrgChart Now** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![OrgChart Now в списке результатов](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OrgChart Now с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в OrgChart Now соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OrgChart Now.

Чтобы настроить и проверить единый вход Azure AD в OrgChart Now, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя OrgChart Now](#create-an-orgchart-now-test-user)** требуется для того, чтобы в OrgChart Now существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении OrgChart Now.

**Чтобы настроить единый вход Azure AD в OrgChart Now, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **OrgChart Now** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения OrgChart Now** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес: `https://sso2.orgchartnow.com`

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` — это идентификатор сущности SAML, скопированный из раздела "Краткий справочник" и описанный далее в этом руководстве.

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_400.png)
    
7. В разделе **Настройка OrgChart Now** щелкните **Настроить OrgChart Now**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML** из **раздела "Краткий справочник"** и используйте его для заполнения поля **URL-адрес входа** в разделе **Домены и URL-адреса приложения OrgChart Now**.

    ![Настройка OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Чтобы настроить единый вход на стороне **OrgChart Now**, отправьте загруженный **XML-файл метаданных** в [службу поддержки OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-orgchart-now-test-user"></a>Создание тестового пользователя OrgChart Now

Чтобы пользователи Azure AD могли выполнять вход в OrgChart Now, они должны быть подготовлены для OrgChart Now. 

1. Приложение OrgChart Now поддерживает JIT-подготовку. Эта функция включена по умолчанию. Пользователь будет создан при попытке получить доступ к приложению OrgChart Now (если он еще не создан). Функция JIT-подготовки пользователей создаст **пользователя только для чтения**, если запрос SSO поступает из распознанного IDP, а в списке пользователей не найдено сообщение электронной почты в утверждении SAML. Для этой функции автоматической подготовки в OrgChart Now необходимо создать группу доступа с названием **Общие**. Чтобы создать группу доступа, выполните приведенные ниже действия.

    a. Перейдите к параметру **Manage Groups** (Управление группами), щелкнув значок **шестеренки** в правом верхнем углу пользовательского интерфейса.

    ![Группы OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manage.png)  

    Б. Щелкните значок **Add** (Добавить) и назовите группу **Общие**, а затем нажмите кнопку **ОК**. 

    ![Добавление OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Выберите папки, к которым необходимо предоставить доступ общим пользователям или пользователям только для чтения.

    ![Папки OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Заблокируйте** папки, чтобы только администраторы могли их изменять. Затем нажмите кнопку **ОК**.

    ![Блокировка OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Для создания **администраторов** и пользователей **с доступом для чтения и записи** необходимо вручную создать пользователя, чтобы получить доступ к их уровню прав через единый вход. Чтобы подготовить учетную запись пользователя, выполните следующие действия.

    a. Войдите в OrgChart Now с правами администратора безопасности.

    Б.  Щелкните **Settings** (Параметры) в верхнем правом углу, а затем выберите **Manage Users** (Управление пользователями).

    ![Параметры OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Щелкните **Add** (Добавить) и сделайте следующее:

    ![Управление OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * В текстовое поле **User ID** (ИД пользователя) введите идентификатор пользователя (например, **brittasimon@contoso.com**).

    * В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя (например, **brittasimon@contoso.com**).

    * Щелкните **Добавить**.
    
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к OrgChart Now.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в OrgChart Now, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **OrgChart Now**.

    ![Ссылка на OrgChart Now в списке приложений](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку OrgChart Now на панели доступа, вы автоматически войдете в приложение OrgChart Now.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с UserVoice | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 41466c73dbd8dec88a1e1e06a907d9686c49efbe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Руководство по интеграции Azure Active Directory с UserVoice

В этом руководстве описано, как интегрировать UserVoice с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением UserVoice обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению UserVoice.
- Вы можете включить автоматический вход пользователей в UserVoice (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением UserVoice, вам потребуется:

- подписка Azure AD;
- подписка UserVoice с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление UserVoice из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-uservoice-from-the-gallery"></a>Добавление UserVoice из коллекции
Чтобы настроить интеграцию UserVoice с Azure AD, необходимо добавить UserVoice из коллекции в список управляемых приложений SaaS.

**Чтобы добавить UserVoice из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **UserVoice**, выберите **UserVoice** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![UserVoice в списке результатов](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение UserVoice с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в UserVoice соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в UserVoice.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в UserVoice.

Чтобы настроить и проверить единый вход Azure AD в UserVoice, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя UserVoice](#create-a-uservoice-test-user)** требуется для того, чтобы в UserVoice существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении UserVoice.

**Чтобы настроить единый вход Azure AD в UserVoice, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **UserVoice** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. В разделе **Домены и URL-адреса приложения UserVoice** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenantname>.UserVoice.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов UserVoice](https://www.uservoice.com/).

4. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток**.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация UserVoice** щелкните **Настроить UserVoice**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Конфигурация UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт UserVoice с учетными данными администратора.

8. На панели инструментов в меню в верхней части экрана щелкните **Параметры**, а затем — **Веб-портал**.
   
    ![Раздел параметров на стороне приложения](./media/active-directory-saas-uservoice-tutorial/ic777519.png "Параметры")

9. На вкладке **Web portal** (Веб-портал) в разделе **User authentication** (Аутентификация пользователя) щелкните **Edit** (Изменить), чтобы открыть диалоговую страницу **Edit User Authentication** (Изменение аутентификации пользователя).
   
    ![Вкладка веб-портала](./media/active-directory-saas-uservoice-tutorial/ic777520.png "Веб-портал")

10. На странице **Изменение проверки подлинности пользователя** выполните следующие действия.
   
    ![Изменение проверки подлинности пользователя](./media/active-directory-saas-uservoice-tutorial/ic777521.png "Изменение проверки подлинности пользователя")
   
    a. Выберите **Единый вход (SSO)**.
 
    Б. Вставьте скопированное на портале Azure значение **URL-адрес службы единого входа SAML** в текстовое поле **SSO Remote Sign-In** (Удаленный единый вход).

    c. Вставьте скопированное на портале Azure значение **URL-адрес выхода** в текстовое поле **SSO Remote Sign-Out** (Удаленный единый выход).
 
    d. В текстовое поле **Current certificate SHA1 fingerprint** (Отпечаток текущего сертификата SHA1) вставьте значение **отпечатка**, которое вы скопировали на портале Azure.
    
    д. Нажмите кнопку **Сохранить параметры проверки подлинности**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-uservoice-test-user"></a>Создание тестового пользователя UserVoice

Чтобы пользователи Azure AD могли выполнять вход в UserVoice, они должны быть подготовлены для UserVoice. В случае с UserVoice подготовка выполняется вручную.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.
1. Войдите в клиента **UserVoice** .

2. Перейдите в меню **Параметры**.
   
    ![Параметры](./media/active-directory-saas-uservoice-tutorial/ic777811.png "Параметры")

3. Выберите пункт **Общие**.

4. Выберите пункт **Агенты и разрешения**.
   
    ![Агенты и разрешения](./media/active-directory-saas-uservoice-tutorial/ic777812.png "Агенты и разрешения")

5. Нажмите кнопку **Добавить администраторов**.
   
    ![Добавление администраторов](./media/active-directory-saas-uservoice-tutorial/ic777813.png "Добавление администраторов")

6. В диалоговом окне **Пригласить администраторов** выполните следующие действия.
   
    ![Пригласить администраторов](./media/active-directory-saas-uservoice-tutorial/ic777814.png "Пригласить администраторов")
   
    a. В текстовом поле Emails ("Адреса электронной почты") введите адрес электронной почты учетной записи, которую вы хотите подготовить, а затем нажмите кнопку **Добавить**.
   
    Б. Нажмите кнопку **Пригласить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя UserVoice или API-интерфейсы, предоставляемые UserVoice для подготовки учетных записей пользователя AAD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к UserVoice.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в UserVoice, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **UserVoice**.

    ![Ссылка на UserVoice в списке "Приложения"](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент UserVoice на панели доступа, вы автоматически войдете в приложение UserVoice.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png


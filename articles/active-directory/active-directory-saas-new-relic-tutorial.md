---
title: Руководство по интеграции Azure Active Directory с New Relic | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: fab53b33f845c277cc9b227141222c468529a75e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352843"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Учебник. Интеграция Azure Active Directory с New Relic

В этом руководстве описано, как интегрировать New Relic с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением New Relic обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к New Relic.
- Вы можете включить автоматический вход пользователей в New Relic (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с New Relic, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа New Relic.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление New Relic из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-new-relic-from-the-gallery"></a>Добавление New Relic из коллекции
Чтобы настроить интеграцию New Relic с Azure AD, необходимо добавить New Relic из коллекции в список управляемых приложений SaaS.

**Чтобы добавить New Relic из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **New Relic**, на панели результатов выберите **New Relic** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![New Relic в списке результатов](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в приложение New Relic с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в New Relic соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в New Relic.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в New Relic.

Чтобы настроить и проверить единый вход Azure AD в New Relic, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя New Relic](#create-a-new-relic-test-user)** требуется для создания в New Relic пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении New Relic.

**Чтобы настроить единый вход Azure AD в New Relic, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **New Relic** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. В разделе **Домены и URL-адреса приложения New Relic** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. В поле **URL-адрес входа** введите URL-адрес, используя следующий шаблон: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`. Не забудьте подставить собственный идентификатор учетной New Relic.

    Б. В текстовом поле **Идентификатор** введите значение `rpm.newrelic.com`.

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. В разделе **Настройка New Relic** щелкните **Настроить New Relic**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. В другом окне веб-браузера войдите на веб-сайт **New Relic** компании в качестве администратора.

8. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/ic797036.png "параметры учетной записи")

9. Щелкните вкладку **Security and authentication** (Безопасность и аутентификация), а затем выберите вкладку **Single sign on** (Единый вход).
   
    ![Единый вход](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Единый вход")

10. На диалоговой странице «Настройка SAML» выполните следующие действия:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Щелкните **Выбор файла** , чтобы передать скачанный сертификат Azure Active Directory.

   Б. В текстовое поле **URL-адрес удаленного входа** вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
   
   c. В текстовое поле **Logout landing URL** (URL-адрес целевой страницы выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

   d. Щелкните **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-new-relic-test-user"></a>Создание тестового пользователя New Relic

Чтобы пользователи Azure Active Directory могли входить New Relic, они должны быть подготовлены в New Relic. В случае New Relic подготовка пользователей осуществляется вручную.

**Чтобы подготовить учетные записи пользователей для New Relic, выполните следующие действия:**

1. Выполните вход на веб-сайт **New Relic** компании в качестве администратора.

2. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/ic797040.png "параметры учетной записи")

3. В области **Account** (Учетная запись) слева щелкните **Summary** (Сводка) и нажмите кнопку **Add user** (Добавить пользователя).
   
    ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/ic797041.png "параметры учетной записи")

4. В диалоговом окне **Активные пользователи** сделайте следующее:
   
    ![Активные пользователи](./media/active-directory-saas-new-relic-tutorial/ic797042.png "Активные пользователи")
   
    a. В текстовом поле **Электронная почта** введите электронный адрес действующего пользователя Azure Active Directory, которого вы хотите подготовить.

    Б. Для параметра **Role** (Роль) выберите значение **User** (Пользователь).

    c. Щелкните **Добавить этого пользователя**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя New Relic или API, предоставляемые New Relic для подготовки учетных записей пользователя AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к New Relic.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в New Relic, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **New Relic**.

    ![Ссылка на New Relic в списке приложений](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент New Relic на панели доступа, вы автоматически войдете в приложение New Relic.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png


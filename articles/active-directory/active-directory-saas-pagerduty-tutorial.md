---
title: Руководство по интеграции Azure Active Directory с PagerDuty | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6ba7e1fc2f58fd8fb7a05935ce2c4b99709d642c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Руководство по интеграции Azure Active Directory с PagerDuty

В этом руководстве описано, как интегрировать PagerDuty с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением PagerDuty обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к PagerDuty.
- Вы можете включить автоматический вход пользователей в PagerDuty (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением PagerDuty, вам потребуется:

- подписка Azure AD;
- подписка PagerDuty с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление PagerDuty из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pagerduty-from-the-gallery"></a>Добавление PagerDuty из коллекции
Чтобы настроить интеграцию PagerDuty с Azure AD, необходимо добавить PagerDuty из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PagerDuty из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **PagerDuty**, выберите **PagerDuty** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в PagerDuty с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в PagerDuty соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PagerDuty.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PagerDuty.

Чтобы настроить и проверить единый вход Azure AD в PagerDuty, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя PagerDuty](#create-a-pagerduty-test-user)** требуется для того, чтобы в PagerDuty существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении PagerDuty.

**Чтобы настроить единый вход Azure AD в PagerDuty, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **PagerDuty** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. В разделе **Домены и URL-адреса приложения PagerDuty** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения PagerDuty](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.pagerduty.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов PagerDuty](https://www.pagerduty.com/support/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pagerduty-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация PagerDuty** щелкните **Настроить PagerDuty**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация PagerDuty](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. В другом окне браузера войдите на свой сайт PagerDuty компании в качестве администратора.

8. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/active-directory-saas-pagerduty-tutorial/ic778535.png "параметры учетной записи")

9. Щелкните **Single Sign-on**(Единый вход).
   
    ![Единый вход](./media/active-directory-saas-pagerduty-tutorial/ic778536.png "Единый вход")

10. На странице **Включить единый вход** выполните следующие действия.
   
    ![Разрешить единый вход](./media/active-directory-saas-pagerduty-tutorial/ic778537.png "Разрешить единый вход")
   
    a. Откройте в Блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).
  
    Б. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
  
    c. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
 
    d. Установите флажок **Включить единый вход**.
 
    д. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-pagerduty-test-user"></a>Создание тестового пользователя PagerDuty

Чтобы пользователи Azure AD могли выполнять вход в PagerDuty, они должны быть подготовлены для PagerDuty.  
В случае с PagerDuty подготовка выполняется вручную.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя PagerDuty или API, предоставляемые PagerDuty для подготовки учетных записей Azure Active Directory.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход в клиент **PagerDuty** .

2. В меню в верхней части страницы щелкните **Пользователи**.

3. Щелкните **Добавить пользователей**.
   
    ![Добавление пользователей](./media/active-directory-saas-pagerduty-tutorial/ic778539.png "добавление пользователей")

4.  В диалоговом окне **Invite your team** (Пригласить команду) выполните следующие действия.
   
    ![Пригласить команду](./media/active-directory-saas-pagerduty-tutorial/ic778540.png "Пригласить команду")

    a. В текстовое поле **First and Last Name** (Имя и фамилия) введите сведения о пользователе, например **Britta Simon**. 
   
    Б. Введите **Email** (Адрес электронной почты) для пользователя, например: **brittasimon@contoso.com**.
   
    c. Нажмите **Add** (Добавить), затем нажмите **Send Invites** (Отправить приглашения).
   
    >[!NOTE]
    >Все добавленные пользователи получат приглашение создать учетную запись PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PagerDuty.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в PagerDuty, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **PagerDuty**.

    ![Ссылка на PagerDuty в списке "Приложения"](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент PagerDuty на панели доступа, вы автоматически войдете в приложение PagerDuty.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_203.png


---
title: Учебник. Интеграция Azure Active Directory с Freshservice | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: a9e50b43308754dae78b0f56c19a539939d19676
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Учебник. Интеграция Azure Active Directory с FreshService

В этом учебнике описано, как интегрировать Freshservice с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Freshservice обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Freshservice.
- Вы можете включить автоматический вход пользователей в Freshservice (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Freshservice, вам потребуется:

- подписка Azure AD;
- подписка Freshservice с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Freshservice из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-freshservice-from-the-gallery"></a>Добавление Freshservice из коллекции
Чтобы настроить интеграцию Freshservice с Azure AD, необходимо добавить Freshservice из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Freshservice из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Freshservice**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_search.png)

5. На панели результатов выберите **Freshservice** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD во Freshservice с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь во Freshservice соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем во Freshservice.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** во Freshservice.

Чтобы настроить и проверить единый вход Azure AD во Freshservice, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Freshservice](#creating-a-freshservice-test-user)** требуется для создания в Freshservice пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Freshservice.

**Чтобы настроить единый вход Azure AD во Freshservice, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Freshservice** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Freshservice** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<democompany>.freshservice.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Freshservice](https://support.freshservice.com/). 
 
4. В разделе **Сертификат подписи SAML** скопируйте значение **отпечатка** сертификата.

    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Freshservice** щелкните **Настроить Freshservice**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Freshservice в качестве администратора.

8. В верхнем меню щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Администратор")

9. В области **Customer Portal** (Клиентский портал) выберите **Security** (Безопасность).
   
    ![Безопасность](./media/active-directory-saas-freshservice-tutorial/ic790815.png "Безопасность")

10. В разделе **Security** (Безопасность) выполните следующие действия.
   
    ![Единый вход](./media/active-directory-saas-freshservice-tutorial/ic790816.png "Единый вход")
   
    a. Включите **единый вход**.

    Б. Выберите **Единый вход SAML**.

    c. В текстовое поле **URL-адрес входа SAML** вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    d. В текстовое поле **URL-адрес выхода SAML** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    д. В текстовое поле **Отпечаток сертификата безопасности** вставьте значение **ОТПЕЧАТОК** сертификата, которое вы скопировали на портале Azure.

    f. Нажмите кнопку **Сохранить**
   
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-freshservice-test-user"></a>Создание тестового пользователя Freshservice

Чтобы пользователи Azure AD могли выполнять вход во Freshservice, они должны быть подготовлены для Freshservice. В случае с FreshService подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный сайт **FreshService** в качестве администратора.

2. В верхнем меню щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Администратор")

3. В разделе **User Management** (Управление пользователями) выберите **Requesters** (Инициаторы запроса).
   
    ![Инициатор запроса](./media/active-directory-saas-freshservice-tutorial/ic790818.png "Инициатор запроса")

4. Нажмите **Новый инициатор запроса**.
   
    ![Создание инициаторов запроса](./media/active-directory-saas-freshservice-tutorial/ic790819.png "Создание инициаторов запроса")

5. В разделе **Новый инициатор запроса** выполните следующие действия.
   
    ![Создание инициатора запроса](./media/active-directory-saas-freshservice-tutorial/ic790820.png "Создание инициатора запроса")   

    a. В соответствующие текстовые поля введите атрибуты **First name** (Имя) и **Email** (Адрес электронной почты) действующей учетной записи Azure Active Directory, которую вы хотите подготовить.

    Б. Выберите команду **Сохранить**.
   
    >[!NOTE]
    >Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
    >  

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя FreshService или API, предоставляемые FreshService для подготовки учетных записей пользователя AAD.
>  

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon во Freshservice, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Freshservice**.

    ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Freshservice на панели доступа, вы автоматически войдете в приложение Freshservice.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_203.png


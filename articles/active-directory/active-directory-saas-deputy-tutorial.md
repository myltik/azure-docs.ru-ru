---
title: Учебник. Интеграция Azure Active Directory с Deputy | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Deputy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b4e1239855756690be7d6c4d1c91eb58ce2aa11f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340851"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Руководство. Интеграция Azure Active Directory с Deputy

В этом руководстве описано, как интегрировать Deputy с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Deputy обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Deputy.
- Вы можете включить автоматический вход пользователей в Deputy (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Deputy, вам потребуется:

- подписка Azure AD;
- подписка Deputy с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Deputy из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-deputy-from-the-gallery"></a>Добавление Deputy из коллекции
Чтобы настроить интеграцию Deputy с Azure AD, необходимо добавить Deputy из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Deputy из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Deputy**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_search.png)

5. На панели результатов выберите **Deputy** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Deputy с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Deputy соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Deputy.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Deputy.

Чтобы настроить и проверить единый вход Azure AD в Deputy, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Deputy](#creating-a-deputy-test-user)** требуется для создания в Deputy пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Deputy.

**Чтобы настроить единый вход Azure AD в Deputy, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Deputy** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домен и URL-адреса приложения Deputy** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs.` |

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your-subdomain>.<region>.deputy.com`
    
    >[!NOTE]
    > Суффикс региона Deputy необязательный, или следует использовать один из следующих: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки Deputy](https://www.deputy.com/call-centers-customer-support-scheduling-software). 

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_general_400.png)
    
7. В разделе **Конфигурация Deputy** щелкните **Настроить Deputy**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_configure.png) 

8. Перейдите по следующему URL-адресу: [https://(ваш_поддомен).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Выберите пункт **Параметры безопасности** и нажмите кнопку **Изменить**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

9. На странице **Параметры безопасности** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)
    
    a. разрешите **вход с помощью учетных записей социальных сетей**;
   
    Б. Откройте в блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Сертификат OpenSSL**.
   
    c. В текстовом поле SAML SSO URL (URL-адрес единого входа) введите `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`.
    
    d. в текстовом поле SAML SSO URL (URL-адрес единого входа) замените `<your subdomain>` на ваш поддомен;
   
    д. В текстовом поле SAML SSO URL (URL-адрес единого входа) замените `<saml sso url>` на **URL-адрес службы единого входа SAML**, скопированный c портала Azure.
   
    f. Нажмите кнопку **Сохранить параметры**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-deputy-test-user"></a>Создание тестового пользователя приложения Deputy

Чтобы пользователи Azure AD могли выполнять вход в Deputy, они должны быть подготовлены для Deputy. В случае с Deputy подготовка выполняется вручную.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.
1. Войдите на сайт Deputy компании в качестве администратора.

2. В верхней части области навигации щелкните **People**(Люди).
   
   ![Люди](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Люди")

3. Нажмите кнопку **Add People** (Добавить людей) и выберите пункт **Add a single person** (Добавить одного человека).
   
   ![Добавить участников](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "добавить участников")

4. Выполните следующие действия и нажмите кнопку **Save & Invite** (Сохранить и пригласить).
   
   ![Новый пользователь](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Новый пользователь")

   a. В текстовом поле **Имя** введите имя, например **BrittaSimon**.
   
   Б. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты той учетной записи Azure AD, которую нужно подготовить.
   
   c. В текстовом поле **Work at** (Место работы) введите название компании.
   
   d. Нажмите кнопку **Save & Invite** (Сохранить и пригласить).

5. Владелец учетной записи AAD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие средства создания учетной записи пользователя Deputy или API, предоставляемые Deputy для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Deputy.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Deputy, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Deputy**.

    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Deputy на панели доступа, вы автоматически войдете в приложение Deputy.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png


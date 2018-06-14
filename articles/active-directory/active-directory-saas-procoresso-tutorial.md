---
title: Руководство. Интеграция Azure Active Directory с Procore SSO | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: eaa340e94311f5e70f473eab534df3fb3a87c866
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348661"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Руководство. Интеграция Azure Active Directory с Procore SSO

В этом руководстве описано, как интегрировать Procore SSO с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Procore SSO обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Procore SSO.
- Вы можете включить автоматический вход пользователей в Procore SSO (единый вход) с помощью учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Procore SSO, вам потребуется:

- подписка Azure AD;
- подписка на Procore SSO с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Procore SSO из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-procore-sso-from-the-gallery"></a>Добавление Procore SSO из коллекции
Чтобы настроить интеграцию Procore SSO с Azure AD, необходимо добавить Procore SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение Procore SSO из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Procore SSO**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. На панели результатов выберите **Procore SSO** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Procore SSO с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Procore SSO соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Procore SSO.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Procore SSO.

Чтобы настроить и проверить единый вход Azure AD в Procore SSO, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Procore SSO](#creating-a-procore-sso-test-user)** требуется для создания пользователя Britta Simon в Procore SSO, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении Procore SSO.

**Чтобы настроить единый вход Azure AD в Procore SSO, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **Procore SSO** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. В разделе **Домен и URL-адреса единого входа в Procore SSO** не нужно выполнять никаких действий, поскольку приложение уже предварительно интегрировано с Azure.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Procore SSO** щелкните **Настроить Procore SSO**, чтобы открыть окно **Настройка единого входа**. Скопируйте **Идентификатор сущности SAML, URL-адрес службы единого входа SAML и URL-адрес единого входа** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Чтобы настроить единый вход на стороне **Procore SSO**, выполните вход на корпоративный сайт Procore с правами администратора.

8. В раскрывающемся списке инструментов щелкните **Admin** (Администрирование), чтобы открыть страницу для настройки единого входа.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Вставьте в поля нужные значения, как описано ниже

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. В поле **Single Sign On Issuer URL** (URL-адрес издателя единого входа), вставьте идентификатор сущности SAML, скопированный на портале Azure.

    Б. В поле **Single Sign On Target URL)** (Целевой URL-адрес единого входа), вставьте URL-адрес службы единого входа SAML, скопированный на портале Azure.

    c. Теперь откройте **XML-файл метаданных**, который вы ранее скачали с портала Azure, и скопируйте из него сертификат, расположенный в теге с именем **X509Certificate**. Вставьте это значение в поле **Single Sign On x509 Certificate** (Сертификат x509 для единого входа).

10. Щелкните **Save Changes** (Сохранить изменения).

11. Когда вы завершите эту настройку, передайте **имя домена** (например, **contoso.com**), для которого осуществляется вход в Procore, в [службу поддержки Procore](https://support.procore.com/) для активации федеративного единого входа для этого домена.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-procore-sso-test-user"></a>Создание тестового пользователя Procore SSO

Выполните следующие действия, чтобы создать тестового пользователя на стороне Procore.

1. Войдите на корпоративный сайт Procore с правами администратора.  

2. В раскрывающемся списке инструментов щелкните **Directory** (Каталог), чтобы открыть страницу корпоративного каталога.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Щелкните **Add a Person** (Добавить сотрудника), чтобы открыть форму регистрации, и введите следующие параметры.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    Б. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя, например **BrittaSimon@contoso.com**.

    d. Для параметра **Permission Template** (Шаблон разрешений) выберите значение **Apply Permission Template Later** (Применить шаблон разрешений позже).

    д. Нажмите кнопку **Создать**.

4. Проверьте и измените при необходимости сведения о добавляемом сотруднике.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Щелкните действие **Save and Send Invitiation** (Сохранить и отправить приглашение), если требуется приглашение, или просто **Save** (Сохранить), чтобы завершить регистрацию пользователя.
    
    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Procore SSO.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложение Procore SSO, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Procore SSO**.

    ![Настройка единого входа](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). Щелкнув плитку Procore SSO на панели доступа, вы автоматически войдете в приложение Procore SSO.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png


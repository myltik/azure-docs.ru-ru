---
title: Руководство по интеграции Azure Active Directory с Adobe Creative Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 808debcee11eb83f7e6afa1f3443a35c9f6767c4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Руководство по интеграции Azure Active Directory с Adobe Creative Cloud

В этом учебнике описано, как интегрировать приложение Adobe Creative Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Adobe Creative Cloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Adobe Creative Cloud.
- Вы можете включить автоматический вход пользователей в Adobe Creative Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Adobe Creative Cloud, вам потребуется:

- подписка Azure AD;
- подписка Adobe Creative Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Adobe Creative Cloud из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Добавление Adobe Creative Cloud из коллекции
Чтобы настроить интеграцию Adobe Creative Cloud с Azure AD, необходимо добавить Adobe Creative Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Creative Cloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Adobe Creative Cloud**, выберите **Adobe Creative Cloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Adobe Creative Cloud в списке результатов](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Adobe Creative Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Adobe Creative Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Creative Cloud.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Adobe Creative Cloud.

Чтобы настроить и проверить единый вход Azure AD в Adobe Creative Cloud, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создайте тестового пользователя Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)** для создания в Adobe Creative Cloud пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении Adobe Creative Cloud.

**Чтобы настроить единый вход Azure AD в Adobe Creative Cloud, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Adobe Creative Cloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, то в разделе **Домены и URL-адреса приложения Adobe Creative Cloud** выполните следующее.

    ![Сведения о домене и URL-адресах единого входа приложения Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.okta.com/saml2/service-provider/<token>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.okta.com/auth/saml20/accauthlinktest`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Adobe Creative Cloud](https://helpx.adobe.com/in/contact/support.html). 

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    В текстовом поле **URL-адрес для входа** введите значение `https://adobe.com`.

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибуты пользователя** приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:

    | Имя атрибута | Значение атрибута |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | LastName |user.surname |
    | Email |user.mail |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.
    
    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.
    
8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. В разделе **Конфигурация Adobe Creative Cloud** щелкните **Настройка Adobe Creative Cloud**, чтобы открыть окно **настройки входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. В другом окне веб-браузера войдите в [консоль администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

11. На панели навигации вверху выберите **Параметры**, а затем щелкните **Удостоверение**. Откроется список доменов. Щелкните ссылку **Настройка** для вашего домена. В разделе **Single Sign On Configuration Required** (Обязательная конфигурация единого входа) выполните следующие действия. Дополнительные сведения см. в статье о [настройке домена](https://helpx.adobe.com/enterprise/using/set-up-domain.html).

    ![Параметры](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Параметры")
    
    a. Щелкните **Browse** (Просмотр), чтобы найти и передать сертификат, загруженный из Azure AD, в качестве **сертификата поставщика удостоверений**.
    
    Б. В текстовое поле **IDP issuer** (Издатель удостоверений) поместите значение **Идентификатор сущности SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.
    
    c. В текстовое поле **IDP Login URL** (URL-адрес входа поставщика удостоверений) поместите значение **URL-адрес службы единого входа SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.
    
    d. Для параметра **HTTP — Redirect** (Перенаправление HTTP) выберите вариант **IDP Binding** (Привязка к поставщику удостоверений).
    
    д. Для параметра **Email Address** (Адрес электронной почты) выберите вариант **User Login Setting** (Настройки входа пользователя).
    
    f. Нажмите кнопку **Сохранить** .

12. Теперь на панели мониторинга отобразится XML-файл **Download Metadata** (Загрузить метаданные). Он содержит URL-адреса компании Adobe для описания сущности (EntityDescriptor) и URL-адреса службы утверждений (AssertionConsumerService). Откройте этот файл и перенесите настройки в приложение Azure AD.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Используйте значение EntityDescriptor, предоставленное компанией Adobe, в качестве значения для параметра **Identifier** (Идентификатор) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

    Б. Используйте значение AssertionConsumerService, предоставленное компанией Adobe, в качестве значения для параметра **Reply URL** (URL-адрес ответа) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Создание тестового пользователя Adobe Creative Cloud

Чтобы пользователи Azure AD могли входить в Adobe Creative Cloud, они должны быть подготовлены для Adobe Creative Cloud. В случае Adobe Creative Cloud подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.

1. Войдите на сайт [консоли администрирования Adobe](https://adminconsole.adobe.com) от имени администратора.

2. Добавьте пользователя в консоль Adobe с помощью федеративного идентификатора и назначьте его профилю продукта. Подробные сведения о добавлении пользователей см. в статье, посвященной [добавлению пользователей в консоль администрирования Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. На этом этапе введите свой адрес электронной почты или имя участника-пользователя в форму регистрации Adobe, нажмите клавишу TAB, и вы должны попасть обратно в Azure AD через федеративные отношения:
    * Веб-доступ: www.adobe.com > "Вход"
    * В программе классического приложения > "Вход"
    * В приложении > "Справка" > "Вход"

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Adobe Creative Cloud.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Adobe Creative Cloud, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Adobe Creative Cloud**.

    ![Ссылка на Adobe Creative Cloud в списке приложений](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Adobe Creative Cloud на панели доступа, вы автоматически войдете в приложение Adobe Creative Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Настройка домена (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Настройка Azure для использования с единым входом Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png


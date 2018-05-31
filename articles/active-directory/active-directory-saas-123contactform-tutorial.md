---
title: Руководство по интеграции Azure Active Directory с 123ContactForm | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 5f7ad606fc8ee394125300a55dc0586a055a33be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341436"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Руководство по интеграции Azure Active Directory с 123ContactForm

В этом руководстве описано, как интегрировать 123ContactForm с Azure Active Directory (Azure AD).

Интеграция 123ContactForm с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к 123ContactForm.
- Вы можете включить автоматический вход пользователей в 123ContactForm (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с 123ContactForm, вам потребуется:

- подписка Azure AD;
- подписка 123ContactForm с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление 123ContactForm из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-123contactform-from-the-gallery"></a>Добавление 123ContactForm из коллекции
Чтобы настроить интеграцию 123ContactForm с Azure AD, необходимо добавить 123ContactForm из коллекции в список управляемых приложений SaaS.

**Чтобы добавить 123ContactForm из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **123ContactForm**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. На панели результатов выберите **123ContactForm** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в 123ContactForm с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в 123ContactForm соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в 123ContactForm.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в 123ContactForm.

Чтобы настроить и проверить единый вход Azure AD в 123ContactForm, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя 123ContactForm](#creating-a-123contactform-test-user)** нужно для того, чтобы в 123ContactForm также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении 123ContactForm.

**Чтобы настроить единый вход Azure AD в 123ContactForm, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **123ContactForm** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, то в разделе **Домены и URL-адреса приложения 123ContactForm** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`.

4. Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. Щелкните параметр **Показать дополнительные параметры URL-адресов**.

    Б. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Необходимо будет обновить эти значения, указав фактические URL-адреса и идентификатор. Это описывается далее в этом руководстве.
    
5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **123ContactForm**, перейдите по адресу [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **BrittaSimon@Contoso.com**.

    Б. Щелкните **Upload** (Передать) и выберите XML-файл метаданных, скачанный с портала Azure.

    c. Щелкните **SUBMIT FORM** (Отправить форму).

8. На странице **Microsoft Azure AD - Single sign-on - Configure App Settings** (Microsoft Azure AD — единый вход — настройка параметров приложения) выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, скопируйте значение **IDENTIFIER** (Идентификатор) для своего экземпляра и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения 123ContactForm** на портале Azure.
    
    Б. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, скопируйте значение **REPLY URL** (URL-адрес ответа) для своего экземпляра и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения 123ContactForm** на портале Azure.

    c. Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, скопируйте значение **SIGN ON URL** (URL-адрес для входа) для своего экземпляра и вставьте его в текстовое поле **URL-адрес для входа** в разделе **Домены и URL-адреса приложения 123ContactForm** на портале Azure.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-123contactform-test-user"></a>Создание тестового пользователя 123ContactForm

Приложение поддерживает JIT-подготовку пользователей, поэтому после проверки подлинности пользователи будут созданы в приложении автоматически.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к 123ContactForm.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в 123ContactForm, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **123ContactForm**.

    ![Настройка единого входа](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "123ContactForm" на панели доступа, вы автоматически войдете в приложение 123ContactForm.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png


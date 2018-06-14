---
title: Руководство по интеграции Azure Active Directory с Questetra BPM Suite | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 122473da723cb101e0f0f9226b34aa3294477657
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351925"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Учебник. Интеграция Azure Active Directory с Questetra BPM Suite

В этом руководстве описано, как интегрировать Questetra BPM Suite с Azure Active Directory (Azure AD).

Интеграция Questetra BPM Suite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Questetra BPM Suite.
- Вы можете включить автоматический вход пользователей в Questetra BPM Suite (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Questetra BPM Suite, вам потребуется следующее:

- подписка Azure AD;
- подписка Questetra BPM Suite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Questetra BPM Suite из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Добавление Questetra BPM Suite из коллекции
Чтобы настроить интеграцию Questetra BPM Suite в Azure AD, необходимо добавить Questetra BPM Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Questetra BPM Suite из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Questetra BPM Suite**, выберите **Questetra BPM Suite** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Добавление из коллекции](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Questetra BPM Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Questetra BPM Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Questetra BPM Suite.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Questetra BPM Suite.

Чтобы настроить и проверить единый вход Azure AD в Questetra BPM Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)** требуется для того, чтобы в Questetra BPM Suite существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Questetra BPM Suite.

**Чтобы настроить единый вход Azure AD в Questetra BPM Suite, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Questetra BPM Suite** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Параметр "Вход на основе SAML"](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Questetra BPM Suite** сделайте следующее.

    ![Раздел "Домены и URL-адреса приложения Questetra BPM Suite"](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Вы можете получить эти значения в разделе **SP Information** (Сведения о SP) на своем корпоративном сайте **Questetra BPM Suite**. Как это сделать, описывается далее в этом руководстве. Вы можете также обратиться к [группе поддержки клиентов Questetra BPM Suite](https://www.questetra.com/contact/). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Certificate (Base 64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить"](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Questetra BPM Suite** щелкните **Настроить Questetra BPM Suite**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Раздел "Конфигурация Questetra BPM Suite"](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. В другом окне веб-браузера войдите в свой корпоративный веб-сайт **Questetra BPM Suite** в качестве администратора.

8. В верхнем меню щелкните пункт **Системные параметры**. 
   
    ![единого входа Azure AD][10]

9. Чтобы открыть страницу **SingleSignOnSAML**, щелкните **SSO (SAML)** (Единый вход (SAML)). 
   
    ![единого входа Azure AD][11]

10. На своем корпоративном сайте **Questetra BPM Suite** в разделе **SP Information** (Сведения о SP) выполните следующие действия.

    a. Скопируйте значение **ACS URL** (URL-адрес ACS) и вставьте его в текстовое поле **URL-адрес для входа** в разделе **Домены и URL-адреса приложения Questetra BPM Suite** на портале Azure.
    
    Б. Скопируйте значение **Entity ID** (Идентификатор сущности) и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения Questetra BPM Suite** на портале Azure.

11. На своем корпоративном сайте **Questetra BPM Suite** выполните следующие действия. 
   
    ![Настройка единого входа][15]
   
    a. Выберите пункт **Включить единый вход**.
   
    Б. В текстовое поле **Entity ID** (Идентификатор сущности) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
    
    c. В текстовое поле **Sign-in page URL** (URL-адрес страницы входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
    
    d. В текстовое поле **Sign-out page URL** (URL-адрес страницы выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
    
    д. В текстовом поле **NameID format** (Формат идентификатора имени) введите `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Откройте в Блокноте сертификат в кодировке **Base64**, скачанный с портала Azure, скопируйте его содержимое в буфер обмена и вставьте в текстовое поле **Validation certificate** (Сертификат проверки). 

    ж. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Создание тестового пользователя Questetra BPM Suite

Цель этого раздела — создать пользователя с именем Britta Simon в Questetra BPM Suite.

**Чтобы создать пользователя с именем Britta Simon в Questetra BPM Suite, выполните следующие действия.**

1. Войдите на свой корпоративный сайт Questetra BPM Suite в качестве администратора.
2. Выберите **System Settings > User List > New User** (Системные параметры > Список пользователей > Новый пользователь). 
3. В диалоговом окне создания нового пользователя выполните следующие действия: 
   
    ![Создание тестового пользователя][300] 
   
    a. В текстовое поле **Name** (Имя) введите **имя** пользователя **britta.simon@contoso.com**.
   
    Б. В текстовое поле **Email** (Адрес электронной почты) введите **адрес электронной почты** пользователя **britta.simon@contoso.com**.
   
    c. В текстовое поле **Password** (Пароль) введите **пароль** для пользователя.
    
    d. Нажмите кнопку **Добавить нового пользователя**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы разрешите пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Questetra BPM Suite.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Questetra BPM Suite, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Questetra BPM Suite**.

    ![Questetra BPM Suite в списке приложений](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Questetra BPM Suite на панели доступа, вы автоматически войдете в приложение Questetra BPM Suite.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 


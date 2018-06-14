---
title: Руководство по интеграции Azure Active Directory с SAP NetWeaver | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: 9ef63353286a43afed9c8a909e121a75fc8a14c5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346587"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Руководство. Интеграция Azure Active Directory с SAP NetWeaver

В этом руководстве описано, как интегрировать SAP NetWeaver с Azure Active Directory.

Интеграция SAP NetWeaver с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к приложению SAP NetWeaver.
- Вы можете включить автоматический вход пользователей в SAP NetWeaver (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SAP NetWeaver, вам потребуется:

- подписка Azure AD;
- подписка SAP NetWeaver с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP NetWeaver из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sap-netweaver-from-the-gallery"></a>Добавление SAP NetWeaver из коллекции
Чтобы настроить интеграцию SAP NetWeaver с Azure AD, необходимо добавить SAP NetWeaver из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP NetWeaver из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SAP NetWeaver**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_search.png)

5. На панели результатов выберите **SAP NetWeaver** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SAP NetWeaver с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP NetWeaver соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP NetWeaver.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP NetWeaver.

Чтобы настроить и проверить единый вход Azure AD в SAP NetWeaver, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP NetWeaver](#creating-an-sap-netweaver-test-user)** требуется для создания пользователя Britta Simon в SAP NetWeaver, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP NetWeaver.

**Чтобы настроить единый вход Azure AD в SAP NetWeaver, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **SAP NetWeaver** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_samlbase.png)

3. В разделе **Домен и URL-адреса приложения SAP NetWeaver** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your company instance of SAP NetWeaver>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<your company instance of SAP NetWeaver>`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<your company instance of SAP NetWeaver>/sap/saml2/sp/acs/100`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора, URL-адреса ответа и URL-адреса входа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов SAP NetWeaver](https://www.sap.com/support.html). 

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_400.png)
    
6. В разделе **Настройка SAP NetWeaver** щелкните **Настроить SAP NetWeaver**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_configure.png) 

7. Для настройки единого входа на стороне приложения **SAP NetWeaver** необходимо отправить скачанные **метаданные в формате XML** и **идентификатор сущности SAML** в [службу поддержки SAP NetWeaver](https://www.sap.com/support.html). 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Name** (Имя) введите **Britta Simon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-sap-netweaver-test-user"></a>Создание тестового пользователя SAP NetWeaver

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP NetWeaver. Чтобы добавить пользователей на платформу SAP NetWeaver, обратитесь в [службу поддержки SAP NetWeaver](https://www.sap.com/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SAP NetWeaver.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении SAP NetWeaver, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP NetWeaver**.

    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP NetWeaver на панели доступа, вы автоматически войдете в приложение SAP NetWeaver.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_203.png


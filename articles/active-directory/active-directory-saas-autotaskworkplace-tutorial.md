---
title: Руководство по интеграции Azure Active Directory с Autotask Workplace | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Autotask Workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 1468d48c7a60176638d6c0be1a0072b2e5e1ba82
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347879"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Руководство по интеграции Azure Active Directory с Autotask Workplace

В этом руководстве описано, как интегрировать Autotask Workplace с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Autotask Workplace обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Autotask Workplace.
- Вы можете включить автоматический вход пользователей в Autotask Workplace (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Autotask Workplace, вам потребуется:

- подписка Azure AD;
- подписка Autotask Workplace с поддержкой единого входа.
- права администратора или суперадминистратора в рабочей области;
- учетная запись администратора в Azure AD;
- учетные записи в Workplace и Azure AD (адреса электронной почты для обоих должны совпадать) для пользователей, которые будут применять этот компонент.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Autotask Workplace из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-autotask-workplace-from-the-gallery"></a>Добавление Autotask Workplace из коллекции
Чтобы настроить интеграцию Autotask Workplace с Azure AD, необходимо добавить Autotask Workplace из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Autotask Workplace из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Autotask Workplace**, на панели результатов выберите **Autotask Workplace** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Autotask Workplace в списке результатов](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Autotask Workplace с использованием тестового пользователя Britta Simon.

Для настройки единого входа Azure AD необходимо знать, какой пользователь в Autotask Workplace соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Autotask Workplace.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Autotask Workplace.

Чтобы настроить и проверить единый вход Azure AD в Autotask Workplace, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Autotask Workplace](#create-an-autotask-workplace-test-user)** нужно для того, чтобы в Autotask Workplace также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Autotask Workplace.

**Чтобы настроить единый вход Azure AD в Autotask Workplace, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Autotask Workplace** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **IdP**, то в разделе **Домены и URL-адреса приложения Autotask Workplace** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для IDP приложения Autotask Workplace](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Сведения о домене и URL-адресах единого входа для IDP приложения Autotask Workplace](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_400.png)

7. В другом окне браузера войдите в Workplace Online, используя учетные данные администратора.

    >[!Note]
    >При настройке поставщика удостоверений нужно указать поддомен. Чтобы убедиться, что указан правильный поддомен, войдите в Workplace Online. После входа обратите внимание на поддомен в URL-адресе
    >(это частью между https:// и .awp.autotask.net/). Это должен быть домен us, eu, ca или au.

8. Последовательно выберите **Конфигурация** > **Единый вход** и сделайте следующее:

    ![Конфигурация единого входа в Autotask](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Выберите **XML Metadata File** (XML-файл метаданных), а затем отправьте **XML-файл метаданных**, скачанный с портала Azure.

    Б. Нажмите кнопку **Enable SSO** (Включить единый вход).
    
    ![Подтверждение конфигурации единого входа в Autotask](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Установите флажок **I confirm this information is correct and I trust this IdP** (Я подтверждаю эти сведения и доверяю этому поставщику удостоверений).

    d. Нажмите кнопку **Утвердить**.
     
>[!Note]
>Если вам требуется помощь по настройке Autotask Workplace и учетной записи Workplace, см. [эту страницу](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-an-autotask-workplace-test-user"></a>Создание тестового пользователя Autotask Workplace

В этом разделе описано, как создать пользователя Britta Simon в приложении Autotask. Обратитесь к [группе поддержки Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm), чтобы добавить пользователей в платформу Autotask Workplace.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Autotask Workplace.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Autotask Workplace, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Autotask Workplace**.

    ![Ссылка Autotask Workplace в списке приложений](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Autotask Workplace" на панели доступа, вы автоматически войдете в приложение Autotask Workplace.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png


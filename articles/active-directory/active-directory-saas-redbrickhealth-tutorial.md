---
title: Руководство по интеграции Azure Active Directory с RedBrick Health | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и RedBrick Health.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 8d51d516d8716c0ac07bc851210733d352c47b6a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349759"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Руководство по интеграции Azure Active Directory с RedBrick Health

В этом руководстве описано, как интегрировать RedBrick Health с Azure Active Directory (Azure AD).

Интеграция Azure AD с RedBrick Health обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к RedBrick Health.
- Вы можете включить автоматический вход пользователей в RedBrick Health (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с RedBrick Health, вам потребуется:

- подписка Azure AD;
- подписка на RedBrick Health с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление RedBrick Health из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-redbrick-health-from-the-gallery"></a>Добавление RedBrick Health из коллекции
Чтобы настроить интеграцию RedBrick Health с Azure AD, необходимо добавить RedBrick Health из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RedBrick Health из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **RedBrick Health**, выберите **RedBrick Health** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![RedBrick Health в списке результатов](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в RedBrick Health с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в RedBrick Health соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и его представлением в RedBrick Health.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в RedBrick Health.

Чтобы настроить и проверить единый вход Azure AD в RedBrick Health, выполните следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RedBrick Health](#create-a-redbrick-health-test-user)** требуется для того, чтобы в RedBrick Health существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении RedBrick Health.

**Чтобы настроить единый вход Azure AD в RedBrick Health, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **RedBrick Health** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. В разделе **Домены и URL-адреса приложения RedBrick Health** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `http://www.redbrickhealth.com`
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Для рабочей среды: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Щелкните **Показать дополнительные параметры URL-адресов**.
    
    ![Сведения о домене и URL-адресах единого входа приложения RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`.
    
    > [!NOTE] 
    > Значение "Состояние ретранслятора" приведено для примера. Вместо него нужно указать фактическое значение состояния ретранслятора. Чтобы получить это значение, обратитесь в [службу поддержки RedBrick Health](https://home.redbrickhealth.com/contact/).

4. Приложение RedBrick Health ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Эти утверждения зависят от конкретного клиента и от ваших требований. Указанные ниже необязательные утверждения являются только примерами, которые можно настроить для приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения.

    ![Настройка единого входа](./media/active-directory-saas-redbrickhealth-tutorial/attribute.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:

    | Имя атрибута | Значение атрибута |
    | ---------------| ----------------|
    | Имя субъекта | ********** |
    | Идентификатор клиента | ********** |
    | Идентификатор субъекта | ********** |
    
    > [!NOTE]
    > Цены указаны исключительно в ознакомительных целях. Вам необходимо определить атрибуты в соответствии с требованиями вашей организации. Обратитесь в [службу поддержки RedBrick Health](https://home.redbrickhealth.com/contact/), чтобы получить дополнительные сведения о необходимых утверждениях.
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.
    
    ![Настройка единого входа](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

6. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_400.png)

8. В разделе **Настройка RedBrick Health** щелкните **Настроить RedBrick Health**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Настройка RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Для настройки единого входа на стороне **RedBrick Health** необходимо отправить скачанный **сертификат (Base64)** и **идентификатор сущности SAML** [службе поддержки RedBrick Health](https://home.redbrickhealth.com/contact/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-redbrick-health-test-user"></a>Создание тестового пользователя RedBrick Health

В этом разделе описано, как создать пользователя Britta Simon в приложении RedBrick Health. Обратитесь в [службу поддержки RedBrick Health](https://home.redbrickhealth.com/contact/), чтобы добавить пользователей на платформу RedBrick Health. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к RedBrick Health.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению RedBrick Health, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **RedBrick Health**.

    ![Ссылка на RedBrick Health в списке "Приложения"](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку RedBrick Health на панели доступа, вы автоматически войдете в приложение RedBrick Health.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_203.png


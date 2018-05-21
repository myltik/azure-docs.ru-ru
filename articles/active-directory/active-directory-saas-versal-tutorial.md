---
title: Руководство по интеграции Azure Active Directory с Versal | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Versal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: a2710c7fa0d035cee8a16d10edf3d603443ad520
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Руководство. Интеграция Azure Active Directory с Versal

В этом руководстве описано, как интегрировать Versal с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Versal обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Versal.
- Вы можете включить автоматический вход пользователей в Versal (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Versal, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Versal.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Versal из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-versal-from-the-gallery"></a>Добавление Versal из коллекции
Чтобы настроить интеграцию Versal с Azure AD, необходимо добавить Versal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Versal из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Versal**, выберите **Versal** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Versal в списке результатов](./media/active-directory-saas-versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Versal с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Versal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Versal.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Versal.

Чтобы настроить и проверить единый вход Azure AD в Versal, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Versal](#create-a-versal-test-user)** требуется для того, чтобы в Versal существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Versal.

**Чтобы настроить единый вход Azure AD в Versal, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Versal** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-versal-tutorial/tutorial_versal_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Versal** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Versal](./media/active-directory-saas-versal-tutorial/tutorial_versal_url.png)

    a. В текстовом поле **Идентификатор** введите значение `VERSAL`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://versal.com/sso/saml/orgs/<organization_id>`.

    > [!NOTE] 
    > Значение URL-адреса ответа приведено для примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь к [группе поддержки Versal](https://support.versal.com/hc/).
    
4. Приложение ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. По умолчанию **идентификатор пользователя** имеет значение **user.userprincipalname**, но для **Versal** требуется сопоставить это значение с адресом электронной почты пользователя. Для этого можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации.
    
    ![Раскрывающееся меню идентификатора пользователя](./media/active-directory-saas-versal-tutorial/tutorial_versal_attribute.png)

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-versal-tutorial/tutorial_versal_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-versal-tutorial/tutorial_general_400.png)
    
7. Для настройки единого входа на стороне **Versal** необходимо отправить скачанный **XML-файл метаданных** и **сертификат подписи SAML** в [службу поддержки Versal](https://support.versal.com/hc/). Специалисты службы поддержки настроят подключение единого входа SAML в организации Versal на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-versal-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-versal-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-versal-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-versal-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-versal-test-user"></a>Создание тестового пользователя Versal

В этом разделе описано, как создать пользователя Britta Simon в приложении Versal. Чтобы создать пользователя Britta Simon в вашей организации, следуйте указаниям в руководстве [Creating a SAML test user for Microsoft Azure](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) (Создание тестового пользователя SAML для Microsoft Azure). Перед использованием единого входа необходимо создать и активировать пользователей в приложении Versal. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Versal, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Versal, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Versal**.

    ![Ссылка на Versal в списке "Приложения"](./media/active-directory-saas-versal-tutorial/tutorial_versal_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью курса Versal, внедренного на веб-сайт.
Сведения о внедрении курса Versal с поддержкой единого входа Azure AD см. в руководстве по поддержке **единого входа SAML** [Embedding Organizational Courses](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) (Внедрение организационных курсов). 

Вам понадобится создать курс, предоставить к нему доступ пользователям вашей организации и опубликовать его для тестирования внедрения курса. Дополнительные сведения см. в статьях о [создании курса](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [его публикации](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), а также об [управлении учебными курсами](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management).  
                     

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-versal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-versal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-versal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-versal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-versal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-versal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-versal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-versal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-versal-tutorial/tutorial_general_203.png


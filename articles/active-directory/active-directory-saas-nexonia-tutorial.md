---
title: Руководство по интеграции Azure Active Directory с Nexonia | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Nexonia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a93b771a-9bc3-444a-bdc0-457f8bb7e780
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 027b7ae5b87cc8349670b2270124c18d68242a9f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-nexonia"></a>Руководство по интеграции Azure Active Directory с Nexonia

В этом руководстве описано, как интегрировать Nexonia с Azure Active Directory.

Интеграция Azure AD с приложением Nexonia обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Nexonia.
- Вы можете включить автоматический вход пользователей в Nexonia (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Nexonia, вам потребуется:

- подписка Azure AD;
- подписка на Nexonia с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Nexonia из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-nexonia-from-the-gallery"></a>Добавление Nexonia из коллекции
Чтобы настроить интеграцию Nexonia с Azure AD, необходимо добавить Nexonia из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Nexonia из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Nexonia**, на панели результатов выберите **Nexonia** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Nexonia в списке результатов](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Nexonia с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD, необходимо знать, какой пользователь в Nexonia соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nexonia.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Nexonia.

Чтобы настроить и проверить единый вход Azure AD в Nexonia, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Nexonia](#create-a-nexonia-test-user)** требуется для того, чтобы в Nexonia существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Nexonia.

  > [!Note]
   > Если при интеграции возникают проблемы, воспользуйтесь этой [ссылкой](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery) для ознакомления с руководством по устранению неполадок. Если вам по-прежнему не удается найти решение, направьте запрос в службу поддержки через портал Azure.

**Чтобы настроить единый вход Azure AD в Nexonia, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Nexonia** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Nexonia** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Nexonia](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_url.png)

    a. В текстовом поле **Идентификатор** введите значение: `Nexonia`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://system.nexonia.com/assistant/saml.do?orgCode=<organizationcode>`.

    > [!NOTE] 
    > Значение URL-адреса ответа приведено для примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь к [группе поддержки Nexonia](https://nexonia.zendesk.com/hc/requests/new).
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Nexonia** щелкните **Настроить Nexonia**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Nexonia](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_configure.png) 

7. Чтобы настроить единый вход на стороне **Nexonia**, нужно отправить скачанный **сертификат (Base64), URL-адрес выхода, идентификатор сущности SAML, URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** [группе поддержки Nexonia](https://nexonia.zendesk.com/hc/requests/new). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-nexonia-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-nexonia-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-nexonia-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-nexonia-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-nexonia-test-user"></a>Создание тестового пользователя Nexonia

В этом разделе описано, как создать пользователя Britta Simon в приложении Nexonia. Обратитесь в [службу поддержки Nexonia](https://nexonia.zendesk.com/hc/requests/new), чтобы добавить пользователей на платформу Nexonia. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Nexonia.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Nexonia, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Nexonia**.

    ![Ссылка на Nexonia в списке "Приложения"](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Nexonia на панели доступа, вы автоматически войдете в приложение Nexonia.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_203.png


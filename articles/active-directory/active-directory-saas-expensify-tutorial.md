---
title: Учебник. Интеграция Azure Active Directory с Expensify | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: jeedes
ms.openlocfilehash: ed9589c5963532e06d73670cc6c7d45ef0b3f78d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350599"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Руководство. Интеграция Azure Active Directory с Expensify

В этом руководстве описано, как интегрировать Expensify с Azure Active Directory (Azure AD).

Интеграция Expensify с Azure AD дает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Expensify.
- Вы можете включить автоматический вход пользователей в Expensify (единый вход) с помощью их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Expensify, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Expensify.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Expensify из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-expensify-from-the-gallery"></a>Добавление Expensify из коллекции
Чтобы настроить интеграцию Expensify с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Expensify из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Expensify**, выберите **Expensify** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Expensify в списке результатов](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Expensify с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Expensify соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Expensify.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Expensify.

Чтобы настроить и проверить единый вход Azure AD в Expensify, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Expensify](#create-an-expensify-test-user)** требуется для создания в Expensify пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Expensify.

**Чтобы настроить единый вход Azure AD в Expensify, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Expensify** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Expensify** выполните следующие действия:

    ![Сведения о домене и URL-адресах для единого входа в приложение Expensify](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_url.png)

    a. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес: `https://www.expensify.com/authentication/saml/login`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.<companyname>.expensify.com`

    > [!NOTE] 
    > Замените часть `<companyname>` URL-адреса идентификатора доменным именем вашей компании. Ознакомьтесь с примером `https://contoso.expensify.com` выше. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Expensify](mailto:help@expensify.com).

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_general_400.png)

6. Чтобы включить единый вход в Expensify, сначала необходимо включить в этом приложении **управление доменами**. Это можно сделать, выполнив действия, указанные [здесь](http://help.expensify.com/domain-control). Для получения дополнительной поддержки обратитесь в [службу поддержки клиентов Expensify](mailto:help@expensify.com). После включения управления доменами сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png)
    
    a. Войдите в приложение Expensify.
    
    Б. На панели инструментов в верхней части экрана нажмите **Администратор**.
    
    c. На панели слева щелкните **Домен**.
    
    d. Щелкните имя проверенного домена.
    
    д. На панели слева щелкните **SAML**, а затем переведите переключатель в положение **Включено**.
    
    f. Откройте скачанные метаданные федерации из Azure AD в блокноте, скопируйте содержимое и вставьте его в текстовое поле **Метаданные поставщика удостоверений**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-expensify-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-expensify-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-expensify-test-user"></a>Создание тестового пользователя Expensify

В этом разделе описано, как создать пользователя Britta Simon в приложении Expensify. Обратитесь в [службу поддержки клиентов Expensify](mailto:help@expensify.com), чтобы добавить пользователей на платформу Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Expensify.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Expensify, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Expensify**.

    ![Ссылка на Expensify в списке "Приложения"](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Expensify на панели доступа, вы автоматически войдете в приложение Expensify.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png


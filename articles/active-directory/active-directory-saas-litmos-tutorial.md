---
title: Учебник. Интеграция Azure Active Directory с Litmos | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8314497e37c074ab346079b7758d8b3680f2d6e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Руководство по интеграции Azure Active Directory с Litmos

В этом руководстве описано, как интегрировать приложение Litmos с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Litmos обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Litmos.
- Вы можете включить автоматический вход пользователей в Litmos (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Litmos, вам потребуется:

- подписка Azure AD;
- подписка Litmos с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Litmos из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-litmos-from-the-gallery"></a>Добавление Litmos из коллекции
Чтобы настроить интеграцию Litmos с Azure AD, необходимо добавить Litmos из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Litmos из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Litmos**, выберите **Litmos** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Litmos в списке результатов](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Litmos с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Litmos соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Litmos.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Litmos.

Чтобы настроить и проверить единый вход Azure AD в Litmos, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Litmos](#create-a-litmos-test-user)** требуется для того, чтобы в Litmos существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Litmos.

**Чтобы настроить единый вход Azure AD в Litmos, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Litmos** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Litmos** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Litmos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.litmos.com/account/Login`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.litmos.com/integration/samllogin`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Вместо этих значений укажите фактические идентификатор и URL-адрес ответа, к которым мы вернемся позже в этом руководстве, или обратитесь к [группе поддержки Litmos](https://www.litmos.com/contact-us/), чтобы получить данные значения.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. В рамках конфигурации необходимо настроить **атрибуты токена SAML** для приложения Litmos.

    ![Раздел "Атрибут"](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Имя атрибута   | Значение атрибута |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |user.mail |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Диалоговое окно "Добавление атрибута"](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.     

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. В другом окне браузера войдите на корпоративный сайт Litmos в качестве администратора.

8. В области навигации слева щелкните **Accounts**(Учетные записи).
   
    ![Раздел "Accounts" (Учетные записи) на стороне приложения][22] 

9. Щелкните вкладку **Integrations** (Интеграции).
   
    ![Вкладка "Integrations" (Интеграция)][23] 

10. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**.
   
    ![Раздел "SAML 2.0"][24] 

11. Скопируйте значение в разделе **The SAML endpoint for litmos is** (Конечная точка SAML для Litmos) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Litmos** на портале Azure. 
   
    ![Конечная точка SAML][26] 

12. В приложении **Litmos** выполните следующие действия.
    
     ![Приложение Litmos][25] 
     
     a. Выберите команду **Enable SAML**(Включить SAML).
    
     Б. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **SAML X.509 Certificate** (Сертификат SAML X.509).
     
     c. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-litmos-test-user"></a>Создание тестового пользователя Litmos

Цель этого раздела — создать пользователя с именем Britta Simon в Litmos.  
Приложение Litmos поддерживает JIT-подготовку. Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.

**Чтобы создать пользователя с именем Britta Simon в Litmos, выполните следующие действия.**

1. В другом окне браузера войдите на корпоративный сайт Litmos в качестве администратора.

2. В области навигации слева щелкните **Accounts**(Учетные записи).
   
    ![Раздел "Accounts" (Учетные записи) на стороне приложения][22] 

3. Щелкните вкладку **Integrations** (Интеграции).
   
    ![Вкладка "Integrations" (Интеграция)][23] 

4. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**.
   
    ![SAML 2.0][24] 
    
5. Установите флажок **Autogenerate Users** (Автоматически создавать пользователей).
   
    ![Автоматическое создание пользователей][27] 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Litmos.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Litmos, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Litmos**.

    ![Ссылка на Litmos в списке "Приложения"](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент Litmos на панели доступа, вы автоматически войдете в приложение Litmos. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png


---
title: "Руководство по интеграции Azure Active Directory с Replicon | Документация Майкрософт"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Replicon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 130e13b0bb801c498f6de6e4bfd61bd3d2c3bf00
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Учебник. Интеграция Azure Active Directory с Replicon

В этом учебнике вы узнаете, как интегрировать Replicon с Azure Active Directory (Azure AD).

Интеграция Replicon с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к Replicon.
- Позволяет пользователям автоматически получить вошедшего в Replicon (Single Sign-On) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Replicon, необходимы следующие элементы:

- подписка Azure AD;
- Replicon единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Replicon из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-replicon-from-the-gallery"></a>Добавление Replicon из галереи
Для настройки интеграции Replicon в Azure AD, необходимо добавить Replicon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Replicon из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Replicon**выберите **Replicon** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Replicon в списке результатов](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с учетом тестового пользователя с именем «Britta Simon» Replicon.

Azure AD для единого входа для работы, необходимо знать пользователя аналога в Replicon с пользователем в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в Replicon необходимо установить.

В Replicon, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с Replicon, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Replicon](#create-a-replicon-test-user)**  — на аналог Саймон Britta в Replicon, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в Replicon приложения.

**Чтобы настроить Azure AD единого входа с Replicon, выполните следующие действия.**

1. На портале Azure на **Replicon** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_samlbase.png)

3. На **URL-адреса и домена Replicon** выполните следующие действия:

    ![URL-адреса и replicon домена единого входа сведения](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://global.replicon.com/<companyname>`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://global.replicon.com/!/saml2/<companyname>/sso/post`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Обратитесь к [группа поддержки клиента Replicon](https://www.replicon.com/customerzone/contact-support) для получения этих значений. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-replicon-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на сайт Replicon своей компании в качестве администратора.

7. Для настройки SAML 2.0 выполните следующие действия:
   
    ![Включение аутентификации SAML](./media/active-directory-saas-replicon-tutorial/ic777805.png "Включение аутентификации SAML")
    
    a. Для отображения **EnableSAML Authentication2** диалоговое окно, добавив следующие URL-адресу, после ключа вашей компании:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
    
    * Ниже показана схема полного URL-адреса.  
   `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
   
   Б. Щелкните **+**, чтобы развернуть раздел **v20Configuration**.

   c. Щелкните **+**, чтобы развернуть раздел **metaDataConfiguration**.

   d. Щелкните **Выбрать файл**, чтобы выбрать XML-файл метаданных поставщика удостоверений, и нажмите кнопку **Отправить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-replicon-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-replicon-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-replicon-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-replicon-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-replicon-test-user"></a>Создание тестового пользователя Replicon

Чтобы пользователи Azure AD могли выполнять вход в Replicon, они должны быть подготовлены для Replicon. В случае с Replicon подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. В окне веб-браузера войдите на сайт Replicon своей компании в качестве администратора.

2. Выберите **Administration \> Users** ("Администрирование" > "Пользователи").
   
    ![Пользователи](./media/active-directory-saas-replicon-tutorial/ic777806.png "Пользователи")

3. Нажмите кнопку **+ Добавить пользователя**.
   
    ![Добавление пользователя](./media/active-directory-saas-replicon-tutorial/ic777807.png "Добавление пользователя")

4. В разделе **Профиль пользователя** сделайте следующее:
   
    ![Профиль пользователя](./media/active-directory-saas-replicon-tutorial/ic777808.png "Профиль пользователя")
    
    a. В **имя входа** в текстовое поле введите адрес электронной почты пользователя Azure AD, вы хотите подготовить как  **BrittaSimon@contoso.com** .
    
    Б. Для параметра **Authentication Type** (Тип аутентификации) выберите значение **SSO** (Единый вход).
    
    c. В текстовом поле **Отдел** введите отдел пользователя.

    d. Для параметра **Employee Type** (Тип сотрудника) выберите значение **Administrator** (Администратор).

    д. Нажмите кнопку **Сохранить профиль пользователя**.

>[!NOTE]
>Можно использовать любые другие Replicon пользователя средства создания учетных записей или интерфейсы API, предоставляемые Replicon для подготовки учетных записей Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к Replicon.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Replicon, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Replicon**.

    ![Replicon ссылку в список приложений](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки Replicon плитки на панели доступа, вы должны получить автоматически вошедшего в приложение Replicon.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_203.png


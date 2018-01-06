---
title: "Учебник: Интеграция Azure Active Directory с помощью строки WORKS | Документы Microsoft"
description: "Дополнительные сведения о настроить единый вход между Azure Active Directory и работает строки."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: b15f5d02a7baff0bc56f2928f73822214c595ab0
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Учебник: Интеграция Azure Active Directory с WORKS строки

В этом учебнике вы узнаете, как интегрировать строки работает с Azure Active Directory (Azure AD).

Интеграция строки работает с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, кто имеет доступ к СТРОКЕ работает.
- Вы можете включить их учетные записи Azure AD пользователям автоматически получить вошедшего в СТРОКЕ WORKS (Single Sign-On).
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Настройка интеграции Azure AD с WORKS строки, необходимы следующие элементы:

- подписка Azure AD;
- СТРОКИ работает единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление строки WORKS из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-line-works-from-the-gallery"></a>Добавление строки WORKS из галереи
Для настройки интеграции строки работает в Azure AD, необходимо добавить строки WORKS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить строки WORKS из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **WORKS строки**выберите **WORKS строки** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![СТРОКИ работает в списке результатов](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с строки работает основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать для пользователя — пользователь аналога в СТРОКЕ работает в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в СТРОКЕ работает необходимо установить.

В СТРОКЕ работает, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с WORKS строки, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя WORKS строки](#create-a-line-works-test-user)**  — иметь аналог Саймон Britta в СТРОКЕ работает, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении работает строки.

**Чтобы настроить Azure AD единого входа с WORKS строки, выполните следующие действия.**

1. На портале Azure на **WORKS строки** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_samlbase.png)

3. На **URL-адреса и домена работает строки** выполните следующие действия:

    ![URL-адреса и домена работает строки единого входа сведения](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://auth.worksmobile.com/d/login/{domain}/?userId={ID@domain}`

    Б. В текстовом поле **Идентификатор** введите значение `worksmobile.com`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [группа поддержки клиента работает строки](mailto:dl_ssoinfo@worksmobile.com) для получения этого значения.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютер.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-lineworks-tutorial/tutorial_general_400.png)

6. На **конфигурации WORKS строки** щелкните **Настройка WORKS строки** открыть **Настройка входа** окна. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка WORKS ЛИНИИ](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_configure.png) 

7. Для настройки единого входа на **WORKS строки** стороны, необходимо отправить загруженного **файл, URL-адрес выхода, идентификатор сущности SAML и SAML единого входа URL-адрес службы сертификата** для [поддерживает WORKS строки команда](mailto:dl_ssoinfo@worksmobile.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-lineworks-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-lineworks-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-lineworks-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-lineworks-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-line-works-test-user"></a>Создание тестового пользователя WORKS строки

В этом разделе создайте пользователя с именем Britta Simon в СТРОКЕ работает. Можно работать с [строки WORKS поддержки](mailto:dl_ssoinfo@worksmobile.com) для добавления пользователей в платформе WORKS строки.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к СТРОКЕ работает.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta WORKS строки, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **WORKS строки**.

    ![Связь работает строки в списке приложений](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки строки WORKS плитки на панели доступа, вы должны получить автоматически вошедшего в приложение работает строки.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_203.png


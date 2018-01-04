---
title: "Учебник: Интеграция Azure Active Directory с Cisco Webex | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Cisco Webex."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Учебник: Интеграция Azure Active Directory с Cisco Webex

В этом учебнике вы узнаете, как интегрировать Cisco Webex с Azure Active Directory (Azure AD).

Интеграция Cisco Webex с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к Cisco Webex.
- Позволяет пользователям автоматически получить входить в Cisco Webex с их учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Cisco Webex, необходимы следующие элементы:

- подписка Azure AD;
- Подписка единого входа с включенным Cisco Webex

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Cisco Webex из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="add-cisco-webex-from-the-gallery"></a>Добавление Cisco Webex из коллекции
Для настройки интеграции Cisco Webex в Azure AD, необходимо добавить Cisco Webex из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Webex из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Webex**. 

5. Выберите **Cisco Webex** из панели «результаты». Выберите **добавить** кнопку, чтобы добавить приложение.

    ![Cisco Webex в списке результатов](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с Cisco Webex основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать, кто является пользователем аналога в Cisco Webex с пользователем в Azure AD. Другими словами необходимо установить связь между пользователем Azure AD и связанных пользователей в Cisco Webex.

В Cisco Webex ценны **Username** то же значение, как **имя пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями. 

Для настройки и проверки Azure AD единого входа с Cisco Webex, выполните следующие стандартные блоки.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Cisco Webex](#create-a-cisco-webex-test-user) на аналог Саймон Britta в Cisco Webex, связанного с представлением Azure AD пользователя.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в Cisco Webex приложения.

**Чтобы настроить Azure AD единого входа с Cisco Webex, выполните следующие действия:**

1. На портале Azure на **Cisco Webex** странице интеграции приложений выберите **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить единый вход, в **единого входа** в диалоговом **режим** раскрывающемся списке выберите **входа на базе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. В **URL-адреса и домена Cisco Webex** статьи, выполните следующие действия:

    ![URL-адреса и домена Cisco Webex единого входа сведения](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.webex.com`.

    Б. В **идентификатор** введите URL-адрес `http://www.webex.com`.

    c. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Обновите эти значения с URL-адрес ответа фактический и URL-адрес входа. Обратитесь к [группа поддержки клиента Cisco Webex](https://www.webex.co.in/support/support-overview.html) для получения этих значений. 

5. На **сертификат подписи SAML** выберите **метаданные в формате XML**, а затем сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Щелкните **Сохранить**.

    ![Настройка единого входа кнопку Сохранить](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. В **конфигурации Cisco Webex** выберите **Настройка Cisco Webex** Открытие **Настройка входа** окна. Скопируйте **URL-адрес выхода**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. В другом окне браузера Войдите на сайт компании Cisco Webex с правами администратора.

8. В меню в верхней части страницы выберите **Администрирование сайта**.

    ![Администрирование сайта](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Администрирование сайта")

9. В **Управление сайтом** выберите **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "Настройка единого входа")

10. В **федеративного единого входа веб-конфигурация** статьи, выполните следующие действия:
   
    ![Настройка федеративного единого входа](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Настройка федеративного единого входа")  

    a. В **протокол федерации** выберите **SAML 2.0**.

    Б. Для **профиль единого входа**выберите **, инициируемая SP**.

    c. Откройте загруженный сертификат в Блокноте и скопируйте его содержимое.

    d. Выберите **импортировать метаданные SAML**, а затем вставьте скопированного содержимого сертификата.

    д. В **издатель для SAML (ИД IdP)** вставьте значение **идентификатор сущности SAML** , скопированный из портала Azure.

    f. В **URL-адрес службы Единого входа клиента** вставьте **SAML единого входа URL-адрес службы**, который вы скопировали на портале Azure.

    ж. В списке **NameID Format** (Формат элемента NameID) выберите пункт **Адрес электронной почты**.

    h. В **AuthnContextClassRef** введите **urn: oasis: имена: tc: SAML:2.0:ac:classes:Password**.

    i. В **URL-адрес выхода службы единого входа клиента** вставьте **URL-адрес выхода**, который вы скопировали на портале Azure.
   
    j. Выберите **обновление**.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из **Active Directory** > **корпоративных приложений** выберите **Single Sign-On** вкладку, а затем открыть внедренный Документация по **конфигурации** в нижней. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-cisco-webex-test-user"></a>Создание тестового пользователя Cisco Webex

Чтобы пользователи Azure AD для входа в Cisco Webex, их необходимо подготовить в Cisco Webex. В случае с Cisco Webex подготовка выполняется вручную.

**Чтобы предоставить учетной записи пользователя, выполните следующие действия:**

1. Войдите в ваш **Cisco Webex** клиента.

2. Последовательно выберите пункты **Управление пользователями** > **добавить пользователя**.
   
    ![Добавление пользователей](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Добавление пользователей")

3. В **добавить пользователя** статьи, выполните следующие действия:
   
    ![Добавление пользователя](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Добавление пользователя")   

    a. Для **тип учетной записи**выберите **узла**.

    Б. В **имя** введите имя пользователя (в этом случае **Britta**).

    c. В **Фамилия** введите фамилию пользователя (в этом случае **Simon**).

    d. В **Username** введите адреса электронной почты пользователя (в этом случае  **Brittasimon@contoso.com** ).

    д. В **электронной почты** введите адреса электронной почты пользователя (в этом случае  **Brittasimon@contoso.com** ).

    f. В поле **Password** (Пароль) введите пароль пользователя.

    ж. В **Подтверждение** пароль поле, еще раз введите пароль пользователя.

    h. Выберите **Добавить**.

>[!NOTE]
>Можно использовать другие средства создания учетных записей пользователей в Cisco Webex или интерфейсы API, предоставляемые Cisco Webex для подготовки учетных записей Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите пользователя Саймон Britta для использования Azure единого входа путем предоставления им доступа к Cisco Webex.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Cisco Webex, выполните следующие действия:**

1. На портале Azure откройте представление "Приложения". Далее, перейдите к представлению каталога, а затем в **корпоративных приложений**.  

2. Выберите **Все приложения**.

    ![Назначение пользователя][201] 

3. В списке приложений выберите **Cisco Webex**.

    ![По ссылке Cisco Webex в список приложений](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Выберите **пользователей и групп** в **добавить назначение** диалоговое окно.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. Нажмите кнопку **Назначить** в диалоговом окне **Добавление назначения**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При выборе Cisco Webex плитки на панели доступа, вы автоматически получите входить в Cisco Webex приложения.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png


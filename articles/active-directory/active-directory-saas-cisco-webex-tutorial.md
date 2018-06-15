---
title: Руководство. Интеграция Azure Active Directory с Cisco Webex | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 8655340f803ec5a976d482939e666f69cf900812
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234994"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Руководство. Интеграция Azure Active Directory с Cisco Webex

В этом руководстве описано, как интегрировать Cisco Webex с Azure Active Directory (Azure AD).

Интеграция Azure AD с Cisco Webex обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Cisco Webex;
- вы можете включить автоматический вход пользователей в Cisco Webex с помощью учетной записи Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Cisco Webex, вам потребуется:

- подписка Azure AD;
- подписка Cisco Webex с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление Cisco Webex из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="add-cisco-webex-from-the-gallery"></a>Добавление Cisco Webex из коллекции
Чтобы настроить интеграцию Cisco Webex с Azure AD, необходимо добавить Cisco Webex из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Webex из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Webex**. 

5. Выберите **Cisco Webex** в области результатов. Нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Cisco Webex в списке результатов](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cisco Webex с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Cisco Webex соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cisco Webex.

Значение **имени пользователя** в Cisco Webex должно соответствовать **имени пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями. 

Чтобы настроить и проверить единый вход Azure AD в Cisco Webex, выполните инструкции ниже:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Cisco Webex](#create-a-cisco-webex-test-user) нужно для того, чтобы в Cisco Webex также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Cisco Webex.

**Чтобы настроить единый вход Azure AD в Cisco Webex, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Cisco Webex** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** из раскрывающегося списка **Режим** выберите пункт **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Cisco Webex** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.webex.com`.

    Б. В поле **Идентификатор** введите следующий URL-адрес: `http://www.webex.com`.

    c. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями URL-адреса ответа и URL-адреса входа. Чтобы получить их, обратитесь в [службу поддержки клиентов Cisco Webex](https://www.webex.co.in/support/support-overview.html). 

5. В разделе **Сертификат подписи SAML** выберите **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Cisco Webex** выберите **Настроить Cisco Webex**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Cisco Webex в качестве администратора.

8. В верхнем меню выберите **Администрирование веб-сайта**.

    ![Администрирование сайта](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Администрирование сайта")

9. В разделе **Управление сайтом** выберите **SSO Configuration** (Настройка единого входа).
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "Настройка единого входа")

10. В разделе **Federated Web SSO Configuration** (Настройка федеративного единого входа в Интернете) выполните следующие действия:
   
    ![Настройка федеративного единого входа](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Настройка федеративного единого входа")  

    a. В списке **Federation Protocol** (Протокол федерации) выберите пункт **SAML 2.0**.

    Б. В **профиле единого входа** выберите **SP Initiated** (Инициировано поставщиком услуг).

    c. Откройте сертификат в Блокноте и скопируйте его содержимое.

    d. Выберите **Import SAML Metadata** (Импорт метаданных SAML) и вставьте скопированное содержимое сертификата.

    д. В текстовое поле **Issuer for SAML (IdP ID)** (Издатель для SAML (идентификатор поставщика услуг)) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    f. В поле **Customer SSO Service Login URL** (URL-адрес входа в клиентскую службу единого входа) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    ж. В списке **NameID Format** (Формат элемента NameID) выберите пункт **Адрес электронной почты**.

    h. В поле **AuthnContextClassRef** введите **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    i. В поле **Customer SSO Service Logout URL** (URL-адрес выхода для клиентской службы единого входа) вставьте значение **URL-адреса для выхода**, скопированное на портале Azure.
   
    j. Нажмите кнопку **Обновить**.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**, а затем откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений](https://go.microsoft.com/fwlink/?linkid=845985).

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

Чтобы пользователи Azure AD могли выполнить вход в Cisco Webex, они должны быть подготовлены для Cisco Webex. В случае с Cisco Webex подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, выполните следующие действия:**

1. Войдите в клиент **Cisco Webex**.

2. Последовательно выберите пункты **Управление пользователями** > **Добавить пользователя**.
   
    ![Добавление пользователей](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Добавление пользователей")

3. В разделе **Добавление пользователя** выполните следующие действия:
   
    ![Добавление пользователя](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Добавление пользователя")   

    a. Для параметра **Тип учетной записи** выберите значение **Узел**.

    Б. В поле **Имя** введите имя пользователя (**Britta**).

    c. В поле **Фамилия** введите фамилию пользователя (**Simon**).

    d. В поле **Имя пользователя** введите адрес электронной почты пользователя (**Brittasimon@contoso.com**).

    д. В поле **Электронная почта** введите адрес электронной почты пользователя (**Brittasimon@contoso.com**).

    f. В поле **Password** (Пароль) введите пароль пользователя.

    ж. Повторно введите пароль пользователя в поле **Подтверждение пароля**.

    h. Выберите **Добавить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетной записи пользователя Cisco Webex или API, предоставляемые Cisco Webex для подготовки учетных записей пользователя Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Cisco Webex.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Cisco Webex, сделайте следующее:**

1. На портале Azure откройте представление "Приложения". Далее в представлении каталога выберите пункт **Корпоративные приложения**.  

2. Выберите **Все приложения**.

    ![Назначение пользователя][201] 

3. Из списка приложений выберите **Cisco Webex**.

    ![Ссылка на Cisco Webex в списке "Приложения"](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. Нажмите кнопку **Назначить** в диалоговом окне **Добавление назначения**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Cisco Webex на панели доступа, вы автоматически войдете в приложение Cisco Webex.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

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


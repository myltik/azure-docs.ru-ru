---
title: Руководство по интеграции Azure Active Directory с Zscaler Private Access Administrator | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Zscaler Private Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: c3918e4b54d60d6f609e99e24ed2f4b2995bf80c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353727"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Руководство по интеграции Azure Active Directory с Zscaler Private Access Administrator

В этом руководстве описано, как интегрировать Zscaler Private Access Administrator с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler Private Access Administrator обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Zscaler Private Access Administrator.
- Вы можете включить автоматический вход пользователей в Zscaler Private Access Administrator (единый вход) с их учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Zscaler Private Access Administrator, вам потребуется:

- подписка Azure AD;
- подписка Zscaler Private Access Administrator с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler Private Access Administrator из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Добавление Zscaler Private Access Administrator из коллекции
Чтобы настроить интеграцию Zscaler Private Access Administrator с Azure AD, необходимо добавить Zscaler Private Access Administrator из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler Private Access Administrator из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Zscaler Private Access Administrator**, выберите **Zscaler Private Access Administrator** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler Private Access Administrator в списке результатов](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Zscaler Private Access Administrator с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо указать, какой пользователь в Zscaler Private Access Administrator соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Zscaler Private Access Administrator.

Чтобы настроить и проверить единый вход Azure AD в Zscaler Private Access Administrator, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Zscaler Private Access Administrator](#create-a-zscaler-private-access-administrator-test-user)** требуется для того, чтобы в Zscaler Private Access Administrator существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Zscaler Private Access Administrator.

**Чтобы настроить единый вход Azure AD в Zscaler Private Access Administrator, выполните следующее.**

1. На портале управления Azure на странице интеграции с приложением **Zscaler Private Access Administrator** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **IdP**, то в разделе **Домены и URL-адреса приложения Zscaler Private Access Administrator** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.private.zscaler.com/auth/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.private.zscaler.com/auth/sso`.

    c. Установите флажок **Показать дополнительные параметры URL-адресов**.

    d. В текстовом поле **RelayState** введите значение `idpadminsso`.

4.  Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, выполните следующие действия.

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Обратитесь к [группе поддержки Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket), чтобы получить эти значения.
 
5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. В другом окне веб-браузера войдите на свой корпоративный сайт Zscaler Private Access Administrator в качестве администратора.

8. В верхней части страницы щелкните **Administration** (Администрирование) и перейдите в раздел **AUTHENTICATION** (Аутентификация). Щелкните **IdP Configuration** (Конфигурация IdP).

    ![Администрирование Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. В правом верхнем углу щелкните **Add IdP Configuration** (Добавить конфигурацию IdP). 

    ![Добавление IdP в Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. На странице **Add IdP Configuration** (Добавление конфигурации IdP) выполните следующие действия.
 
    ![Выбор IdP в Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Щелкните **Select File** (Выбрать файл), чтобы передать скачанный файл метаданных из Azure AD и указать его в поле **IdP Metadata File Upload** (Передача файла метаданных IdP).

    Б. Будут прочитаны **метаданные IdP** из Azure AD, а также будут заполнены все поля сведений, как показано ниже.

    ![Конфигурация IdP в Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Для параметра **Single Sign On** (Единый вход) выберите значение **Administrator** (Администратор).

    d. Выберите домен в поле **Domains** (Домены).
    
    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Создание тестового пользователя Zscaler Private Access Administrator

Чтобы пользователи Azure AD могли входить в Zscaler Private Access Administrator, их необходимо подготовить в Zscaler Private Access Administrator. В случае Zscaler Private Access Administrator подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на свой корпоративный сайт Zscaler Private Access Administrator с правами администратора.

2. В верхней части страницы щелкните **Administration** (Администрирование) и перейдите в раздел **AUTHENTICATION** (Аутентификация). Щелкните **IdP Configuration** (Конфигурация IdP).

    ![Администрирование Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Щелкните **Administrators** (Администраторы) из левой части меню.

    ![Администратор Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. В правом верхнем углу щелкните **Add Administrator** (Добавить администратора).

    ![Добавление администратора Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. На странице **Add Administrator** (Добавление администратора) сделайте следующее.

    ![Администратор Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. В текстовом поле **Username** (Имя пользователя) введите адрес электронной почты пользователя, например **BrittaSimon@contoso.com**.

    Б. В текстовом поле **Password** (Пароль) введите пароль.

    c. В текстовом поле **Confirm Password** (Подтверждение пароля) повторно введите пароль.

    d. Для параметра **Role** (Роль) выберите значение **Zscaler Private Access Administrator**.

    д. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **BrittaSimon@contoso.com**.

    f. В текстовом поле **Phone** (Телефон) введите номер телефона.

    ж. В текстовом поле **Timezone** (Часовой пояс) выберите часовой пояс.

    h. Выберите команду **Сохранить**.  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler Private Access Administrator.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Zscaler Private Access Administrator, выполните следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Zscaler Private Access Administrator**.

    ![Ссылка на Zscaler Private Access Administrator в списке "Приложения"](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Zscaler Private Access Administrator" на панели доступа, вы автоматически войдете в приложение Zscaler Private Access Administrator.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png


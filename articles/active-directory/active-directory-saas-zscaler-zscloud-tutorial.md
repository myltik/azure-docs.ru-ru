---
title: Руководство по интеграции Azure Active Directory с Zscaler ZSCloud | Документы Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: b44e6c7c47eec15dab2294ca7ebcb7b7dcff3a4c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Руководство. Интеграция Azure Active Directory с Zscaler ZSCloud

Это руководство описывает, как интегрировать Zscaler ZSCloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler ZSCloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Zscaler ZSCloud.
- Вы можете включить автоматический вход пользователей в Zscaler ZSCloud (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Zscaler ZSCloud, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Zscaler ZSCloud.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler ZSCloud из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Добавление Zscaler ZSCloud из коллекции
Чтобы настроить интеграцию Zscaler ZSCloud с Azure AD, нужно добавить Zscaler ZSCloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler ZSCloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Zscaler ZSCloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. На панели результатов выберите **Zscaler ZSCloud** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
Этот раздел описывает настройку и проверку единого входа Azure AD в Zscaler ZSCloud с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, нужно знать, какой пользователь в Zscaler ZSCloud соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler ZSCloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Zscaler ZSCloud.

Чтобы настроить и проверить единый вход Azure AD в Zscaler ZSCloud, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка параметров прокси-сервера](#configuring-proxy-settings)** нужна для настройки параметров прокси-сервера в Internet Explorer.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)** требуется для создания в Zscaler ZSCloud пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Этот раздел описывает, как включить единый вход Azure AD на портале Azure и настроить его в приложении Zscaler ZSCloud.

**Чтобы настроить единый вход Azure AD в Zscaler ZSCloud, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Zscaler ZSCloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Zscaler ZSCloud** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Zscaler ZSCloud.
    
    > [!NOTE] 
    > Вместо него нужно указать фактический URL-адрес для входа. Для получения этого значения обратитесь в [службу поддержки клиентов Zscaler ZSCloud](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Zscaler ZSCloud** щелкните **настроить Zscaler ZSCloud**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. В другом окне браузера войдите на свой корпоративный сайт Zscaler ZSCloud в качестве администратора.

8. В верхнем меню щелкните **Администрирование**.
   
    ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800206.png "Администрирование")

9. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Manage Users & Authentication** (Управление пользователями и проверкой подлинности).   
            
    ![Управление пользователями и проверкой подлинности](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800207.png "Управление пользователями и проверкой подлинности")

10. В разделе **Выбор параметров проверки подлинности для организации** выполните следующие действия.   
                
    ![Аутентификация](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800208.png "Аутентификация")
   
    a. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.

    Б. Щелкните **Настроить параметры единого входа SAML**.

11. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните указанные ниже действия и нажмите кнопку **Готово**.

    ![Единый вход](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800209.png "Единый вход")
    
    a. Вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) в текстовое поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для проверки подлинности).
    
    Б. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.
    
    c. Чтобы передать скачанный сертификат, щелкните **Zscaler pem**.
    
    d. Выберите параметр **Включить автоматическую подготовку SAML**.

12. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800210.png "Администрирование")
    
    a. Выберите команду **Сохранить**.

    Б. Щелкните **Активировать сейчас**.

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.   
    
     ![Свойства браузера](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769492.png "Свойства браузера")

3. Щелкните вкладку **Подключения** .   
  
     ![Подключения](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769493.png "Подключения")

4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

5. В разделе "Прокси-сервер" выполните следующие действия.   
   
    ![Прокси-сервер](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769494.png "Прокси-сервер")

    a. Установите флажок **Использовать прокси-сервер для локальной сети**.

    Б. В текстовом поле "Адрес" введите **gateway.zscalerone.net**.

    c. В текстовом поле "Порт" введите **80**.

    d. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Создание тестового пользователя Zscaler ZSCloud

Чтобы пользователи Azure AD могли входить в ZScaler ZSCloud, их нужно подготовить для ZScaler ZSCloud.  
В случае с ZScaler ZSCloud подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. Войдите в клиент **ZScaler** .

2. Щелкните **Администрирование**.   
   
    ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781035.png "Администрирование")

3. Щелкните **Управление пользователями**.   
        
     ![Добавление](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Добавление")

4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).
      
    ![Добавление](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Добавление")

5. В разделе "Добавить пользователя" выполните следующие действия.
        
    ![Добавление пользователя](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781038.png "Добавление пользователя")
   
    a. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) действующей учетной записи AAD, которую необходимо подготовить.

    Б. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя ZScaler ZSCloud или API, предоставляемые ZScaler ZSCloud для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Этот раздел описывает, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Zscaler ZSCloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Zscaler ZSCloud, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Zscaler ZSCloud**.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа.

Щелкнув плитку "Zscaler ZSCloud" на панели доступа, вы автоматически войдете в приложение Zscaler ZSCloud.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_203.png


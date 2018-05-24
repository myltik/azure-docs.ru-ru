---
title: Руководство по интеграции Azure Active Directory с Zscaler One | Документация Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 1dcf9dfac84dcc4231660de9efba5af249e7f4db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Руководство по интеграции Azure Active Directory с Zscaler One

Это руководство описывает, как интегрировать Zscaler One с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler One обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Zscaler One.
- Вы можете включить автоматический вход пользователей в Zscaler One (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Zscaler One, вам потребуется:

- подписка Azure AD;
- подписка ZScaler One с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler One из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zscaler-one-from-the-gallery"></a>Добавление Zscaler One из коллекции
Чтобы настроить интеграцию Zscaler One с Azure AD, нужно добавить Zscaler One из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler One из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Zscaler One**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_search.png)

5. На панели результатов выберите **Zscaler One** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Zscaler One с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Zscaler One соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler One.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Zscaler One.

Чтобы настроить и проверить единый вход Azure AD в Zscaler One, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка параметров прокси-сервера](#configuring-proxy-settings)** нужна для настройки параметров прокси-сервера в Internet Explorer.
3. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Zscaler One](#creating-a-zscaler-one-test-user)** требуется для того, чтобы в Zscaler One существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
6. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Zscaler One.

**Чтобы настроить единый вход Azure AD в Zscaler One, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Zscaler One** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Zscaler One** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_url.png)

    В текстовом поле "URL-адрес входа" введите URL-адрес, с помощью которого пользователи входят в приложение Zscaler One.

    > [!NOTE] 
    > Вместо него нужно указать фактический URL-адрес для входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Zscaler One](https://www.zscaler.com/company/contact).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Zscaler One** щелкните **настроить Zscaler One**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный сайт Zscaler One в качестве администратора.

8. В верхнем меню щелкните **Администрирование**.
   
    ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/ic800206.png "Администрирование")

9. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Manage Users & Authentication** (Управление пользователями и проверкой подлинности).   
            
    ![Управление пользователями и проверкой подлинности](./media/active-directory-saas-zscaler-one-tutorial/ic800207.png "Управление пользователями и проверкой подлинности")

10. В разделе **Выбор параметров проверки подлинности для организации** выполните следующие действия.   
                
    ![Аутентификация](./media/active-directory-saas-zscaler-one-tutorial/ic800208.png "Аутентификация")
   
    a. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.

    Б. Щелкните **Настроить параметры единого входа SAML**.

11. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните указанные ниже действия и нажмите кнопку **Готово**.

    ![Единый вход](./media/active-directory-saas-zscaler-one-tutorial/ic800209.png "Единый вход")
    
    a. Вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure, в текстовое поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для аутентификации).
    
    Б. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.
    
    c. Чтобы передать скачанный сертификат, щелкните **Zscaler pem**.
    
    d. Выберите параметр **Включить автоматическую подготовку SAML**.

12. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/ic800210.png "Администрирование")
    
    a. Выберите команду **Сохранить**.

    Б. Щелкните **Активировать сейчас**.

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.   
    
     ![Свойства браузера](./media/active-directory-saas-zscaler-one-tutorial/ic769492.png "Свойства браузера")

3. Щелкните вкладку **Подключения** .   
  
     ![Подключения](./media/active-directory-saas-zscaler-one-tutorial/ic769493.png "Подключения")

4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

5. В разделе "Прокси-сервер" выполните следующие действия.   
   
    ![Прокси-сервер](./media/active-directory-saas-zscaler-one-tutorial/ic769494.png "Прокси-сервер")

    a. Установите флажок **Использовать прокси-сервер для локальной сети**.

    Б. В текстовом поле "Адрес" введите **gateway.zscalerone.net**.

    c. В текстовом поле "Порт" введите **80**.

    d. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-zscaler-one-test-user"></a>Создание тестового пользователя Zscaler One

Чтобы пользователи Azure AD могли выполнять вход в Zscaler One, их необходимо подготовить в Zscaler One. В случае Zscaler One подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. Войдите в клиент **Zscaler One** .

2. Щелкните **Администрирование**.   
   
    ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/ic781035.png "Администрирование")

3. Щелкните **Управление пользователями**.   
        
     ![Добавление](./media/active-directory-saas-zscaler-one-tutorial/ic781036.png "Добавление")

4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).
      
    ![Добавление](./media/active-directory-saas-zscaler-one-tutorial/ic781037.png "Добавление")

5. В разделе "Добавить пользователя" выполните следующие действия.
        
    ![Добавление пользователя](./media/active-directory-saas-zscaler-one-tutorial/ic781038.png "Добавление пользователя")
   
    a. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) для действительной учетной записи Azure AD, которую необходимо подготовить.

    Б. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Zscaler One или API, предоставляемые Zscaler One для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Zscaler One.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Zscaler One, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Zscaler One**.

    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Zscaler One" на панели доступа, вы автоматически войдете в приложение Zscaler One.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_203.png


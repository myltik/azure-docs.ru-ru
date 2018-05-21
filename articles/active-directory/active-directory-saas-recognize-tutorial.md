---
title: Руководство по интеграции Azure Active Directory с Recognize | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 5bf235983af2caf753c4df9fa8d70b05bb3b8b23
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Руководство по интеграции Azure Active Directory с Recognize

В этом руководстве описано, как интегрировать Recognize с Azure Active Directory (Azure AD).

Интеграция Recognize с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Recognize;
- вы можете включить автоматический вход пользователей в Recognize (единый вход) под учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Recognize, вам потребуется:

- подписка Azure AD;
- подписка Recognize с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Recognize из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-recognize-from-the-gallery"></a>Добавление Recognize из коллекции
Чтобы настроить интеграцию Recognize с Azure AD, необходимо добавить Recognize из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Recognize из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Recognize** .

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. На панели результатов выберите **Recognize** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Recognize с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Recognize соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Recognize.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Recognize.

Чтобы настроить и проверить единый вход Azure AD в Recognize, вам потребуется выполнить следующие стандартные действия.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Recognize](#creating-a-recognize-test-user)** требуется для того, чтобы в Recognize существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Recognize.

**Чтобы настроить единый вход Azure AD в Recognize, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Recognize** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Recognize** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://recognizeapp.com/<your-domain>/saml/sso`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Обратитесь к [группе поддержки клиентов Recognize](mailto:support@recognizeapp.com), чтобы получить URL-адрес входа. Чтобы получить значение идентификатора, можно открыть URL-адрес метаданных поставщика услуг из раздела параметров единого входа, как описано далее в этом руководстве. . 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Recognize** щелкните **Настроить Recognize**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт Recognize с правами администратора.

8. В правом верхнем углу щелкните **Menu** (Меню). Перейдите к пункту **Company Admin** (Администратор компании).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. В левой панели навигации щелкните **Settings**(Параметры).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. В разделе **SSO Settings** (Параметры единого входа) выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. Для параметра **Enable SSO** (Включить единый вход) установите значение **ON** (Включено).

    Б. В текстовое поле **IDP Entity ID** (Идентификатор сущности IdP) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
    
    c. В текстовое поле **Sso target url** (Целевой URL-адрес единого входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
    
    d. В текстовое поле **Slo target url** (Целевой URL-адрес единого выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure. 
    
    д. Откройте скачанный файл **сертификата в кодировке Base64** в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Certificate** (Сертификат).
    
    f. Нажмите кнопку **Сохранить параметры** . 

11. Кроме сведений в разделе **SSO Settings** (Параметры единого входа), сохраните URL-адрес из раздела **Service Provider Metadata url** (URL-адрес метаданных поставщика службы).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. Откройте ссылку с **URL-адресом метаданных** в новом окне браузера, чтобы скачать документ метаданных. Затем скопируйте значение EntityDescriptor (entityID) из файла и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения Recognize** на портале Azure.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-recognize-test-user"></a>Создание тестового пользователя Recognize

Чтобы пользователи Azure AD могли выполнять вход в Recognize, они должны быть подготовлены для Recognize. Для Recognize подготовка выполняется вручную.

Это приложение не поддерживает подготовку SCIM, но предусматривает альтернативный механизм синхронизации для подготовки пользователей. 

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Recognize в качестве администратора.

2. В правом верхнем углу щелкните **Menu** (Меню). Перейдите к пункту **Company Admin** (Администратор компании).

3. В левой панели навигации щелкните **Settings**(Параметры).

4. В разделе **User Sync** (Синхронизация пользователей) выполните следующие действия.
   
   ![Новый пользователь](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Новый пользователь")
   
   a. В поле **Sync Enabled** (Синхронизация включена) выберите **ON** (Включено).
   
   Б. Для параметра **Choose sync provider** (Выбор поставщика синхронизации) выберите значение **Microsoft/Office 365**.
   
   c. Щелкните **Run User Sync** (Запустить синхронизацию пользователей).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Recognize.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Recognize, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Recognize**.

    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Recognize на панели доступа, вы автоматически войдете в приложение Recognize. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png


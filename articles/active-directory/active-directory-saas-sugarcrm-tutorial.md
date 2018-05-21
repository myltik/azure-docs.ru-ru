---
title: Руководство по интеграции Azure Active Directory с SugarCRM | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении SugarCRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: d4133b5881d25b9481e08ba0597485b62e0c52e5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Руководство по интеграции Azure Active Directory с SugarCRM

В этом руководстве описано, как интегрировать SugarCRM с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SugarCRM обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к SugarCRM.
- Вы можете включить автоматический вход пользователей в SugarCRM (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением SugarCRM, вам потребуется:

- подписка Azure AD;
- Подписка SugarCRM с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SugarCRM из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sugar-crm-from-the-gallery"></a>Добавление SugarCRM из коллекции
Чтобы настроить интеграцию SugarCRM с Azure AD, необходимо добавить SugarCRM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SugarCRM из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SugarCRM**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_search.png)

5. На панели результатов выберите **SugarCRM** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SugarCRM с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SugarCRM соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SugarCRM.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SugarCRM.

Чтобы настроить и проверить единый вход Azure AD в SugarCRM, выполните действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SugarCRM](#creating-a-sugar-crm-test-user)** требуется для того, чтобы в Sugar CRM существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении SugarCRM.

**Чтобы настроить единый вход Azure AD в SugarCRM, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **SugarCRM** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SugarCRM** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов SugarCRM](https://support.sugarcrm.com/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация SugarCRM** щелкните **Настроить SugarCRM**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт SugarCRM с учетными данными администратора.

8. Откройте страницу **Администратор**.
   
    ![Администратор](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "Администратор")

9. В разделе **Administration** (Администрирование) щелкните **Password Management** (Управление паролями).
   
    ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/ic795889.png "Администрирование")

10. Установите флажок **Включить проверку подлинности SAML**.
   
    ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/ic795890.png "Администрирование")

11. В разделе **Проверка подлинности SAML** сделайте следующее:
   
    ![Аутентификация SAML](./media/active-directory-saas-sugarcrm-tutorial/ic795891.png "Аутентификация SAML")  
 
    a. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
  
    Б. В текстовое поле **SLO URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
  
    c. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **Сертификат X.509** .
  
    d. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Создание тестового пользователя SugarCRM

Чтобы пользователи Azure AD могли входить в SugarCRM, их необходимо подготовить для SugarCRM.

В случае SugarCRM подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт **SugarCRM** компании в качестве администратора.

2. Откройте страницу **Администратор**.
   
    ![Администратор](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "Администратор")

3. В разделе **Administration** (Администрирование) щелкните **User Management** (Управление пользователями).
   
    ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/ic795893.png "Администрирование")

4. Выберите **Users (Пользователи) \> Create New User (Создать нового пользователя)**.
   
    ![Создание пользователя](./media/active-directory-saas-sugarcrm-tutorial/ic795894.png "Создание пользователя")

5. На вкладке **Профиль пользователя** сделайте следующее:
   
    ![Новый пользователь](./media/active-directory-saas-sugarcrm-tutorial/ic795895.png "Новый пользователь")

    a. В соответствующие текстовые поля введите **имя**, **фамилию** и **электронный адрес** действующей учетной записи Azure Active Directory.
  
6. Для параметра **Status** (Состояние) выберите значение **Active** (Активно).

7. На вкладке «Пароль» выполните следующие действия:
   
    ![Новый пользователь](./media/active-directory-saas-sugarcrm-tutorial/ic795896.png "Новый пользователь")

    a. Введите пароль в соответствующее текстовое поле.

    Б. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей SugarCRM или API, предоставляемые SugarCRM для подготовки учетных записей пользователей AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к SugarCRM.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SugarCRM, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SugarCRM**.

    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SugarCRM на панели доступа, вы автоматически войдете в приложение SugarCRM.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_203.png


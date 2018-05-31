---
title: Руководство по интеграции Azure Active Directory с Adaptive Suite | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Adaptive Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 80067a82744498b273a99caa69e6c12f47100733
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342351"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Руководство. Интеграция Azure Active Directory с Adaptive Suite

В этом руководстве описано, как интегрировать Adaptive Suite с Azure Active Directory (Azure AD).

Интеграция приложения Adaptive Suite с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению Adaptive Suite.
- Вы можете включить автоматический вход пользователей в Adaptive Suite (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Adaptive Suite, вам потребуется следующее:

- подписка Azure AD;
- подписка Adaptive Suite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Adaptive Suite из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adaptive-suite-from-the-gallery"></a>Добавление Adaptive Suite из коллекции
Чтобы настроить интеграцию приложения Adaptive Suite с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения Adaptive Suite из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Adaptive Suite**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. На панели результатов выберите **Adaptive Suite** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Adaptive Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа службе Azure AD нужно знать, какой пользователь в Adaptive Suite соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Adaptive Suite.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Adaptive Suite.

Чтобы настроить и проверить единый вход Azure AD в Adaptive Suite, выполните следующие действия:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Adaptive Suite](#creating-an-adaptive-suite-test-user)** требуется для создания в Adaptive Suite пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Adaptive Suite.

**Настройка единого входа Azure AD в Adaptive Suite**

1. На портале Azure на странице интеграции с приложением **Adaptive Suite** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Adaptive Suite** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    >[!NOTE]
    > Этот адрес можно узнать на странице **Параметры единого входа SAML** клиента Adaptive Suite.
    >
    
    Щелкните **Показать дополнительные параметры URL-адресов**. В текстовом поле **Identifier (Entity ID)** (Идентификатор (ИД сущности)) введите же URL-адрес, введенный в текстовом поле **URL-адрес ответа**.
    
    >[!NOTE]
    > Идентификатор сущности для каждого домена Adaptive Insights совпадает с его URL-адресом ответа.
    >

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Adaptive Suite** щелкните **Настроить Adaptive Suite**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. В другом окне веб-браузера войдите на корпоративный сайт Adaptive Suite с правами администратора.

8. Откройте страницу **Администратор**.
   
    ![Администратор](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Администратор")

9. В разделе **Users and Roles** (Пользователи и роли) щелкните **Manage SAML SSO Settings** (Управление параметрами единого входа SAML).
   
    ![Управление параметрами единого входа SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Управление параметрами единого входа SAML")

10. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия.
   
    ![Параметры единого входа SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "Параметры единого входа SAML")

    a. Введите имя конфигурации в текстовое поле **Identity provider name** (Имя поставщика удостоверений).
    
    Б. Вставьте значение **идентификатора сущности SAML**, скопированное с портала Azure, в текстовое поле **Identity provider Entity ID** (Идентификатор сущности поставщика удостоверений).
  
    c. Вставьте значение **URL-адреса службы единого входа SAML**, скопированное с портала Azure, в текстовое поле **Identity provider SSO URL** (URL-адрес единого входа поставщика удостоверений).
  
    d. Вставьте значение **URL-адреса службы единого входа SAML**, скопированное с портала Azure, в текстовое поле **Custom logout URL** (Пользовательский URL-адрес для выхода).
  
    д. Чтобы отправить загруженный сертификат, нажмите кнопку **Выбрать файл**.
  
    f. Выберите следующие параметры:
    * Для параметра **SAML user id** (Идентификатор пользователя SAML) выберите значение **User’s Adaptive Insights user name** (Имя пользователя Adaptive Insights).
    * Для параметра **SAML user id location** (Расположение идентификатора пользователя SAML) выберите значение **User id in NameID of Subject** (Идентификатор пользователя из NameID в Subject).
    * Для параметра **SAML NameID format** (Формат NameID SAML) выберите значение **Адрес электронной почты**.
    * Для параметра **Включить SAML** выберите значение **Allow SAML SSO and direct Adaptive Insights login** (Разрешить единый вход SAML и прямой вход Adaptive Insights).
    
    ж. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Создание тестового пользователя Adaptive Suite

Чтобы пользователи Azure AD могли выполнять вход в Adaptive Suite, их следует подготовить в Adaptive Suite.  

* В случае Adaptive Suite подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.** 

1. Выполните вход на корпоративный веб-сайт **Adaptive Suite** в качестве администратора.
2. Откройте страницу **Администратор**.
   
   ![Администратор](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Администратор")
3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Добавить пользователя**.
   
   ![Добавление пользователя](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Добавление пользователя")
4. В разделе **New User** (Новый пользователь) выполните следующие действия.
   
   ![Отправка](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Отправка")   

   a. Введите в текстовые поля **Name** (Имя), **Login** (Имя для входа), **Email** (Адрес электронной почты) и **Password** (Пароль) соответствующие данные действующего пользователя Azure Active Directory, для которого выполняется подготовка.
  
   Б. Выберите **Роль**.
  
   c. Нажмите кнопку **Submit**(Отправить).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Adaptive Suite или API, предоставляемые Adaptive Suite для подготовки учетных записей пользователя AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Adaptive Suite.

![Назначение пользователя][200] 

**Назначение пользователя Britta Simon приложению Adaptive Suite**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Adaptive Suite**.

    ![Настройка единого входа](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Microsoft Azure AD с помощью панели доступа.

Щелкнув плитку Adaptive Suite на панели доступа, вы автоматически войдете в приложение Adaptive Suite.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с Sprinklr | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: e2222e3e71fbddaebf1077149f6d71a43eb8dea0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352758"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Учебник. Интеграция Azure Active Directory с Sprinklr

В этом руководстве описано, как интегрировать Sprinklr с Azure Active Directory (Azure AD).

Интеграция Sprinklr с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Sprinklr.
- Вы можете включить автоматический вход пользователей в Sprinklr (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Sprinklr, вам потребуется:

- подписка Azure AD;
- подписка Sprinklr с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Sprinklr из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sprinklr-from-the-gallery"></a>Добавление Sprinklr из коллекции
Чтобы настроить интеграцию Sprinklr с Azure AD, необходимо добавить Sprinklr из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Sprinklr из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Sprinklr**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_search.png)

5. На панели результатов выберите **Sprinklr** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Sprinklr для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Sprinklr соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sprinklr.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Sprinklr.

Чтобы настроить и проверить единый вход в Azure AD в Sprinklr, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Sprinklr](#creating-a-sprinklr-test-user)** требуется для создания пользователя Britta Simon в Sprinklr, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Sprinklr.

**Чтобы настроить единый вход Azure AD в Sprinklr, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Sprinklr** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. В разделе **Домены и URL-адреса Sprinklr** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.sprinklr.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Sprinklr](https://www.sprinklr.com/contact-us/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sprinklr-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Sprinklr** щелкните **Настроить Sprinklr**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

7. В другом окне веб-браузера войдите на сайт Sprinklr своей организации в качестве администратора.

8. Выберите **Administration (Администрирование) \> Settings (Параметры)**.
   
    ![Администрирование](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Администрирование")

9. В области слева выберите **Manage Partner (Управление партнером) \> Single Sign (Единый вход)**.
   
    ![Управление партнером](./media/active-directory-saas-sprinklr-tutorial/ic782908.png "Управление партнером")

10. Щелкните **+ Добавить параметры единого входа**.
   
    ![Единый вход](./media/active-directory-saas-sprinklr-tutorial/ic782909.png "Единый вход")

11. На странице **Единый вход** сделайте следующее:
   
    ![Единый вход](./media/active-directory-saas-sprinklr-tutorial/ic782910.png "Единый вход")

    a. В текстовом поле **Имя** введите имя конфигурации (например, *WAADSSOTest*).

    Б. Щелкните **Включено**.

    c. Установите флажок **Использовать новый сертификат единого входа**.
             
    д. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений**.

    f. Вставьте значение **Идентификатор сущности SAML**, скопированное на портале Azure, в поле **Идентификатор сущности**.

    ж. Вставьте значение **URL-адреса службы единого входа SAML**, скопированное с портала Azure, в поле **URL-адрес единого входа** поставщика удостоверений.

    h. Вставьте значение **URL-адреса выхода**, скопированное с портала Azure, в поле **URL-адрес выхода поставщика удостоверений**.
     
    i. В поле **SAML User ID Type** (Тип идентификатора пользователя SAML) выберите значение **Assertion contains User”s sprinklr.com username** (Утверждение содержит имя пользователя sprinklr.com).

    j. В поле **SAML User ID Location** (Расположение идентификатора пользователя SAML) выберите значение **User ID is in the Name Identifier element of the Subject statement** (Идентификатор пользователя находится в элементе NameIdentifier оператора Subject).

    k. Выберите команду **Сохранить**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-sprinklr-test-user"></a>Создание тестового пользователя Skilljar

1. Войдите на сайт Sprinklr своей организации в качестве администратора.

2. Выберите **Administration (Администрирование) \> Settings (Параметры)**.
   
    ![Администрирование](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Администрирование")

3. В области слева выберите **Manage Client (Управление клиентом) \> Users (Пользователи)**.
   
    ![Параметры](./media/active-directory-saas-sprinklr-tutorial/ic782914.png "Параметры")

4. Нажмите кнопку **Add User**(Добавить пользователя).
   
    ![Параметры](./media/active-directory-saas-sprinklr-tutorial/ic782915.png "Параметры")

5. В диалоговом окне **Изменение пользователя** сделайте следующее:
   
    ![Изменение пользователя](./media/active-directory-saas-sprinklr-tutorial/ic782916.png "Изменение пользователя") 

    a. В текстовых полях **Email** (Электронная почта), **First Name** (Имя) и **Last Name** (Фамилия) введите данные учетной записи пользователя Azure AD, которую необходимо подготовить.

    Б. Установите флажок **Пароль отключен**.

    c. Выберите **Язык**.

    d. Выберите **Тип пользователя**.

    д. Нажмите кнопку **Обновить**.
   
     >[!IMPORTANT]
     >**Пароль отключен** , чтобы дать пользователю возможность входить через поставщик удостоверений. 
     
6. Перейдите в раздел **Роль**и сделайте следующее:
   
    ![Роли партнера](./media/active-directory-saas-sprinklr-tutorial/ic782917.png "Роли партнера")

    a. В списке **Global** (Глобальные) выберите **ALL\_Permissions**.  

    Б. Нажмите кнопку **Обновить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей Sprinklr или API, предоставляемые Sprinklr для подготовки учетных записей пользователей AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Sprinklr.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon для Sprinklr, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Sprinklr**.

    ![Настройка единого входа](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Sprinklr на панели доступа, вы автоматически войдете в приложение Sprinklr. Дополнительные сведения о панели доступа см. в разделе [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_203.png


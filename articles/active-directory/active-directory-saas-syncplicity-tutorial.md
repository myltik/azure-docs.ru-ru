---
title: Руководство. Интеграция Azure Active Directory с Syncplicity | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 29581fda8cb27989518f6a3d5c69e1cfac763ede
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Учебник. Интеграция Azure Active Directory с Syncplicity

В этом руководстве описано, как интегрировать Syncplicity с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Syncplicity обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Syncplicity.
- Вы можете включить автоматический вход пользователей в Syncplicity (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Syncplicity, вам потребуется:

- подписка Azure AD;
- подписка Syncplicity с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Syncplicity из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-syncplicity-from-the-gallery"></a>Добавление Syncplicity из коллекции
Чтобы настроить интеграцию приложения Syncplicity с Azure AD, вам нужно добавить Syncplicity из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Syncplicity из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Syncplicity**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. На панели результатов выберите **Syncplicity** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Syncplicity с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Syncplicity соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Syncplicity.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Syncplicity.

Чтобы настроить и проверить единый вход Azure AD в Syncplicity, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Syncplicity](#creating-a-syncplicity-test-user)** требуется для создания пользователя Britta Simon в Syncplicity, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Syncplicity.

**Чтобы настроить единый вход Azure AD в Syncplicity, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Syncplicity** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Syncplicity** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.syncplicity.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Syncplicity](https://www.syncplicity.com/contact-us). 
 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Syncplicity** щелкните **Настроить Syncplicity**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Войдите в клиент **Syncplicity** .

8. В меню в верхней части страницы щелкните **Администрирование**, выберите **Параметры**, а затем — **Личный домен и единый вход**.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. На странице диалогового окна **Единый вход** сделайте следующее:
   
    ![Единый вход\(Единый вход\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. В текстовом поле **Личный домен** введите имя своего домена.
  
    Б. В поле **Состояния единого входа** задайте значение **Включено**.

    c. В текстовое поле **Идентификатор сущности** вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    d. В текстовое поле **URL-адрес страницы входа** вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    д. В текстовое поле **URL-адрес выхода** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    f. Напротив пункта **Identity Provider Certificate** (Сертификат поставщика удостоверений) нажмите кнопку **Выбрать файл**, а затем отправьте сертификат, скачанный с портала Azure. 

    ж. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-syncplicity-test-user"></a>Создание тестового пользователя Syncplicity
Чтобы пользователи AAD могли входить систему, их необходимо подготовить для Syncplicity. В этом разделе описывается порядок создания учетных записей пользователей AAD в Syncplicity.

**Чтобы подготовить учетную запись пользователя в Syncplicity, выполните следующее.**

1. Войдите в клиент **Syncplicity** (например: `https://company.Syncplicity.com`).

2. Щелкните **Администрирование** и выберите **Учетные записи пользователей**.

3. Нажмите кнопку **Добавить пользователя**.
   
    ![Управление пользователями](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "Управление пользователями")

4. Введите **адреса электронной почты** учетной записи AAD, которую необходимо подготовить, задайте значение **Пользователь** в поле **Роль**, а затем нажмите кнопку **Далее**.
   
    ![Сведения об учетной записи](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "Сведения об учетной записи")
   
    >[!NOTE]
    >Владелец учетной записи AAD получит электронное сообщение, содержащее ссылку для подтверждения и активации учетной записи. 
    > 

5. Выберите группу в организации, в которую будет входить новый пользователь, а затем нажмите кнопку **Далее**.
   
    ![Участие в группах](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "Участие в группах")
   
    >[!NOTE]
    >Если список групп пуст, просто нажмите кнопку **Далее**. 
    > 

6. Выберите папки, которые будут находиться под управлением Syncplicity на компьютере пользователя, и нажмите кнопку **Далее**.
   
    ![Папки Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Папки Syncplicity")

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Syncplicity или API-интерфейсы, предоставляемые Syncplicity для подготовки учетных записей пользователя AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Syncplicity.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Syncplicity, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Syncplicity**.

    ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Syncplicity на панели доступа, вы автоматически войдете в приложение Syncplicity.
## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png


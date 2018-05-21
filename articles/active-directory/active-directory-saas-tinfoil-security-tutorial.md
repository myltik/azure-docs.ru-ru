---
title: Руководство по интеграции Azure Active Directory с TINFOIL SECURITY | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 96d7b75078fd1075d17d70ee677f28ba1bbb1576
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Руководство по интеграции Azure Active Directory с TINFOIL SECURITY

В этом руководстве описано, как интегрировать TINFOIL SECURITY с Azure Active Directory (Azure AD).

Интеграция TINFOIL SECURITY с Azure AD имеет следующие преимущества:

- С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению TINFOIL SECURITY.
- Вы можете включить автоматический вход пользователей в TINFOIL SECURITY (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением TINFOIL SECURITY, вам потребуется:

- подписка Azure AD;
- подписка TINFOIL SECURITY с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TINFOIL SECURITY из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Добавление TINFOIL SECURITY из коллекции
Чтобы настроить интеграцию TINFOIL SECURITY с Azure AD, вам нужно добавить TINFOIL SECURITY из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TINFOIL SECURITY из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **TINFOIL SECURITY**, выберите **TINFOIL SECURITY** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Добавление TINFOIL SECURITY из коллекции](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в TINFOIL SECURITY с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в TINFOIL SECURITY соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TINFOIL SECURITY.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TINFOIL SECURITY.

Чтобы настроить и проверить единый вход Azure AD в TINFOIL SECURITY, выполните следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TINFOIL SECURITY](#create-a-tinfoil-security-test-user)** требуется для того, чтобы в TINFOIL SECURITY существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TINFOIL SECURITY.

**Чтобы настроить единый вход Azure AD в TINFOIL SECURITY, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **TINFOIL SECURITY** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Вход на основе SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TINFOIL SECURITY** не нужно выполнять никаких действий, так как это приложение предварительно интегрировано с Azure.

    ![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток**.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
    
    ![Атрибуты](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Атрибуты")
    
    | Имя атрибута    |   Значение атрибута |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Щелкните **добавить атрибут пользователя**.
    
    ![Добавление атрибута](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "Атрибуты")
    
    ![Добавление атрибута](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "Атрибуты")
    
    Б. В текстовом поле **Имя атрибута** введите **accountid**.
    
    c. В текстовое поле **Значение атрибута** следует вставить идентификатор учетной записи, который вы получите позже при работе с этим руководством.
    
    d. Нажмите кнопку **ОК**.    

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить"](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация TINFOIL SECURITY** щелкните **Настроить TINFOIL SECURITY**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TINFOIL SECURITY в качестве администратора.

9. На панели инструментов в верхней части экрана щелкните **Моя учетная запись**.
   
    ![Панель мониторинга](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Панель мониторинга")

10. Щелкните **Security**(Безопасность).
   
    ![Безопасность](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "Безопасность")

11. На странице настроек **Единый вход** выполните следующие действия.
   
    ![Единый вход](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "Единый вход")
   
    a. Выберите **Включить SAML**.
   
    Б. Щелкните **Настроить вручную**.
   
    c. В текстовое поле **SAML Post URL** (URL-адрес POST SAML) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    d. В текстовое поле **SAML Certificate Fingerprint** (Отпечаток сертификата SAML) вставьте значение **Отпечаток**, скопированное в разделе **Сертификат подписи SAML**.
  
    д. Скопируйте значение **Your Account ID** (Ваш идентификатор учетной записи) и вставьте его в текстовое поле **Значение атрибута** в разделе **Добавление атрибута** на портале Azure.
   
    f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Выберите "Пользователи и группы" > "Все пользователи". ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Пользователь](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Создание тестового пользователя TINFOIL SECURITY

Чтобы пользователи Azure AD могли выполнить вход в TINFOIL SECURITY, они должны быть подготовлены для TINFOIL SECURITY. В случае TINFOIL SECURITY подготовка выполняется вручную.

**Чтобы подготовить пользователя, выполните следующие действия.**

1. Если пользователь является частью учетной записи Enterprise, то для создания учетной записи пользователя необходимо обратиться к [группе поддержки TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact).

2. Если пользователь является обычным пользователем SaaS TINFOIL SECURITY, то он может добавить участника совместной работы на любой из своих сайтов. Это активирует процесс, который отправит приглашение по указанному электронному адресу для создания учетной записи пользователя TINFOIL SECURITY.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя TINFOIL SECURITY или API, предоставляемые TINFOIL SECURITY для подготовки учетных записей пользователя Azure Active Directory.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TINFOIL SECURITY.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в TINFOIL SECURITY, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **TINFOIL SECURITY**.

    ![Выбор TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "TINFOIL SECURITY" на панели доступа, вы автоматически войдете в приложение TINFOIL SECURITY. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png


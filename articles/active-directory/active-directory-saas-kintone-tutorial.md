---
title: Руководство по интеграции Azure Active Directory с Kintone | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: b2fd0038a192e2ff3d2caef42170372449efc10d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Руководство по интеграции Azure Active Directory с Kintone

В этом руководстве описано, как интегрировать Kintone с Azure Active Directory (Azure AD).

Интеграция Kintone с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Kintone.
- Вы можете включить автоматический вход пользователей в Kintone (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Kintone, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа Kintone

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Kintone из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-kintone-from-the-gallery"></a>Добавление Kintone из коллекции
Чтобы настроить интеграцию Kintone с Azure AD, необходимо добавить Kintone из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kintone из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Kintone**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_search.png)

5. На панели результатов выберите **Kintone** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Kintone для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Kintone соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kintone.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Kintone.

Чтобы настроить и проверить единый вход в Azure AD в Kintone, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Kintone](#creating-a-kintone-test-user)** требуется для создания пользователя Britta Simon в Kintone, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Kintone.

**Чтобы настроить единый вход Azure AD в Kintone, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Kintone** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_samlbase.png)

3. В разделе **Домены и URL-адреса Kintone** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.kintone.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Kintone](https://www.kintone.com/contact/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Kintone** щелкните **Настроить Kintone**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_configure.png) 

7. В другом окне веб-браузера войдите на веб-сайт **Kintone** компании в качестве администратора.

8. Щелкните **Параметры**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/ic785879.png "Параметры")

9. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/ic785880.png "Users & System Administration") (Администрирование пользователей и системы)

10. Перейдите в раздел **System Administration \> Security** (Системное администрирование > Безопасность) и щелкните **Login** (Вход).
   
    ![Login](./media/active-directory-saas-kintone-tutorial/ic785881.png "Login") (Вход)

11. Установите флажок **Включить проверку подлинности SAML**.
   
    ![Аутентификация SAML](./media/active-directory-saas-kintone-tutorial/ic785882.png "Аутентификация SAML")

12. В разделе «Проверка подлинности SAML» выполните следующие действия.
    
    ![Аутентификация SAML](./media/active-directory-saas-kintone-tutorial/ic785883.png "Аутентификация SAML")
    
    a. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
   
    Б. В текстовое поле **URL-адрес выхода SAML** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.
    
    c. Чтобы передать скачанный сертификат, нажмите кнопку **Обзор** .
    
    d. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-kintone-test-user"></a>Создание тестового пользователя Kintone

Чтобы разрешить пользователям Azure AD вход в Kintone, они должны быть подготовлены для Kintone.  
В случае с Kintone подготовка выполняется вручную.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1. Выполните вход на веб-сайт **Kintone** компании в качестве администратора.

2. Щелкните **Параметр**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/ic785879.png "Параметры")

3. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/ic785880.png "Users & System Administration") (Администрирование пользователей и системы)

4. В разделе **User Administration** (Администрирование пользователей) щелкните **Departments & Users** (Отделы и пользователи).
   
    ![Department & Users](./media/active-directory-saas-kintone-tutorial/ic785888.png "Department & Users") (Отделы и пользователи)

5. Щелкните **Новый пользователь**.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/ic785889.png "Новые пользователи")

6. В разделе **New User** (Новый пользователь) выполните следующие действия.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/ic785890.png "Новые пользователи")
   
    a. В соответствующие текстовые поля введите атрибуты **Отображаемое имя**, **Имя входа**, **Новый пароль**, **Подтверждение пароля**, **Адрес электронной почты** и другие данные действующей учетной записи AAD, которую вы хотите подготовить.
 
    Б. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Kintone или API, предоставляемые Kintone для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Kintone.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Kintone, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Kintone**.

    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Kintone на панели доступа, вы автоматически войдете в приложение Kintone.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_203.png


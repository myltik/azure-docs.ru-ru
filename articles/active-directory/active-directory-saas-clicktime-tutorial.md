---
title: Учебник. Интеграция Azure Active Directory с ClickTime | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: f24dab1856880a3d8c69701257167cae8d97e241
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Руководство. Интеграция Azure Active Directory с ClickTime

В этом руководстве описано, как интегрировать ClickTime с Azure Active Directory (Azure AD).

Интеграция ClickTime с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ClickTime.
- Вы можете включить автоматический вход пользователей в ClickTime (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ClickTime, вам потребуется:

- подписка Azure AD;
- подписка ClickTime с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ClickTime из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-clicktime-from-the-gallery"></a>Добавление ClickTime из коллекции.
Чтобы настроить интеграцию ClickTime с Azure AD, необходимо добавить ClickTime из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ClickTime из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ClickTime**, выберите **ClickTime** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ClickTime в списке результатов](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ClickTime с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в ClickTime соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ClickTime.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ClickTime.

Чтобы настроить и проверить единый вход Azure AD в ClickTime, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ClickTime](#create-a-clicktime-test-user)** требуется для создания в ClickTime пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ClickTime.

**Чтобы настроить единый вход Azure AD в ClickTime, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **ClickTime** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. В разделе **Домены и URL-адреса приложения ClickTime** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://app.clicktime.com/sp/`.
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация ClickTime** щелкните **Настроить ClickTime**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт ClickTime в качестве администратора.

8. На панели инструментов в верхней части экрана щелкните **Preferences** (Параметры) и выберите **Security Settings** (Параметры безопасности).

9. В разделе **Настройки единого входа** выполните следующие действия.
   
    ![Параметры безопасности](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Параметры безопасности")
   
    a.  Выберите "**Allow** sign-in using Single Sign-On (SSO) with **Azure AD**" (Разрешить единый вход (SSO) с помощью Azure AD).
   
    Б. В текстовое поле **Identity Provider Endpoint** (Конечная точка входа поставщика удостоверений) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
   
    c.  Откройте в **Блокноте** скачанный с портала Azure**сертификат в кодировке Base-64**, скопируйте его содержимое, а затем вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).
   
    d.  Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-clicktime-test-user"></a>Создание тестового пользователя ClickTime

Чтобы пользователи Azure AD могли выполнить вход в ClickTime, они должны быть подготовлены для ClickTime.  
В случае с ClickTime подготовка выполняется вручную.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетной записи пользователя ClickTime или API, предоставляемые ClickTime для подготовки учетных записей пользователя Azure AD.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**
1. Войдите в клиент **ClickTime** .
2. На панели инструментов в верхней части экрана щелкните **Company** (Компания), а затем — **People** (Пользователи).
   
    ![Люди](./media/active-directory-saas-clicktime-tutorial/tic777282.png "Люди")
3. Нажмите кнопку **Добавить пользователя**.
   
    ![Добавление пользователя](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Добавление пользователя")
4. В разделе "Новый пользователь" выполните следующие действия.
   
    ![Люди](./media/active-directory-saas-clicktime-tutorial/tic777284.png "Люди")
   
    a.  В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например **Britta Simon**. 
  
    Б.  В текстовом поле **Email address** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.
       
    > [!NOTE]
    > При необходимости задайте для нового пользователя дополнительные свойства.
   
    c.  Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ClickTime.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ClickTime, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **ClickTime**.

    ![Ссылка на ClickTime в списке "Приложения"](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ClickTime на панели доступа, вы автоматически войдете в приложение ClickTime.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png


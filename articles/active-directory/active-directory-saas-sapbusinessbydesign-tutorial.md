---
title: Руководство по интеграции Azure Active Directory с SAP Business ByDesign | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 23a0f202cabf39036a3a405894c814655e3f3cd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352224"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Руководство по интеграции Azure Active Directory с SAP Business ByDesign

В этом руководстве описано, как интегрировать SAP Business ByDesign с Azure Active Directory (Azure AD).

Интеграция SAP Business ByDesign с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению SAP Business ByDesign.
- Вы можете включить автоматический вход пользователей в SAP Business ByDesign (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SAP Business ByDesign, вам потребуется:

- подписка Azure AD;
- подписка SAP Business ByDesign с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP Business ByDesign из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Добавление SAP Business ByDesign из коллекции
Чтобы настроить интеграцию SAP Business ByDesign с Azure AD, необходимо добавить SAP Business ByDesign из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Business ByDesign из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP Business ByDesign**, выберите **SAP Business ByDesign** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SAP Business ByDesign в списке результатов](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Business ByDesign с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP Business ByDesign соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Business ByDesign.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP Business ByDesign.

Чтобы настроить и проверить единый вход Azure AD в SAP Business ByDesign, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)** требуется для того, чтобы в SAP Business ByDesign существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении SAP Business ByDesign.

**Чтобы настроить единый вход Azure AD в SAP Business ByDesign, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SAP Business ByDesign** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SAP Business ByDesign** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<servername>.sapbydesign.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html).

4. В разделе **Атрибуты пользователя** выполните следующие действия.

    ![Раздел атрибутов SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. Из списка **Идентификатор пользователя** выберите функцию **ExtractMailPrefix**.
    
    Б. В списке **Почта** выберите атрибут пользователя, который вы хотите использовать в своей реализации. Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.     

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. В разделе **Настройка SAP Business ByDesign** щелкните **Настроить SAP Business ByDesign**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Чтобы настроить единый вход для приложения, выполните следующие действия.
   
    a. Войдите на портал SAP Business ByDesign с правами администратора.
   
    Б. Перейдите к элементу **Application and User Management Common Task** (Общие задачи управления приложением и пользователем) и откройте вкладку **Identity Provider** (Поставщик удостоверений).
   
    c. Щелкните **New Identity Provider** (Новый поставщик удостоверений) и выберите XML-файл метаданных, скачанный с портала Azure. Импортируя метаданные, система автоматически отправляет требуемые сертификаты подписи и шифрования.
   
    ![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Чтобы включить **URL-адрес службы обработчика утверждений** в запрос SAML, выберите **Include Assertion Consumer Service URL** (Включить URL-адрес службы обработчика утверждений).
   
    д. Щелкните **Activate Single Sign-On**(Активировать единый вход).
   
    f. Сохраните изменения.
   
    ж. Перейдите на вкладку **My System** (Моя система).
   
    ![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. В текстовое поле **Azure AD Sign On URL** (URL-адрес входа в Azure AD) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    ![Настройка единого входа](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Укажите, сможет ли сотрудник вручную переключаться между входом с помощью учетных данных (идентификатора пользователя и пароля) и единым входом, выбрав **Manual Identity Provider Selection**(Выбор поставщика удостоверений вручную).
   
    j. В разделе **SSO URL** (URL-адрес единого входа) укажите URL-адрес, который будет использоваться вашими сотрудниками для входа в систему. 
    В раскрывающемся списке URL Sent to Employee (URL-адрес для отправки сотруднику) можно выбрать следующие параметры.
   
    **Non-SSO URL**
   
    Система отправит сотруднику обычный URL-адрес системы. Сотрудник не сможет входить в систему с помощью единого входа. Вместо этого ему необходимо использовать пароль или сертификат.
   
    **SSO URL** 
   
    Система отправит сотруднику URL-адрес единого входа. Сотрудник сможет входить в систему с помощью единого входа. Запрос на проверку подлинности перенаправляется через поставщик удостоверений.
   
    **Automatic Selection**
   
    Если функция единого входа неактивна, система отправит сотруднику обычный URL-адрес системы. Если функция единого входа активна, система проверит, есть ли у сотрудника пароль. При наличии пароля сотруднику будет отправлен URL-адрес единого входа и URL-адрес другого метода входа. Если пароля нет, сотруднику будет отправлен только URL-адрес единого входа.
   
    k. Сохраните изменения.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Создание тестового пользователя SAP Business ByDesign

В этом разделе мы создадим пользователя Britta Simon в приложении SAP Business ByDesign. Чтобы добавить пользователей в SAP Business ByDesign обратитесь в [службу поддержки клиентов SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html). 

> [!NOTE]
> Значение идентификатора имени должно совпадать с полем имени пользователя на платформе SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к приложению SAP Business ByDesign.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SAP Business ByDesign, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Business ByDesign**.

    ![Ссылка на SAP Business ByDesign в списке приложений](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Business ByDesign на панели доступа, вы автоматически войдете в приложение SAP Business ByDesign.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png


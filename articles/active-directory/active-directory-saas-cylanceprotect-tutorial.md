---
title: Руководство. Интеграция Azure Active Directory с CylancePROTECT | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и CylancePROTECT.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jeedes
ms.openlocfilehash: 406ff51e98e1816e1bd041d364313ee2a7b52ca3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342116"
---
# <a name="tutorial-azure-active-directory-integration-with-cylanceprotect"></a>Руководство. Интеграция Azure Active Directory с CylancePROTECT

Из этого руководства вы узнаете, как интегрировать CylancePROTECT с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением CylancePROTECT обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к CylancePROTECT.
- Вы можете включить автоматический вход пользователей в CylancePROTECT (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с CylancePROTECT, вам потребуется:

- подписка Azure AD;
- подписка CylancePROTECT с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление CylancePROTECT из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-cylanceprotect-from-the-gallery"></a>Добавление CylancePROTECT из коллекции.
Чтобы настроить интеграцию CylancePROTECT с Azure AD, необходимо добавить CylancePROTECT из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CylancePROTECT из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **CylancePROTECT**, выберите **CylancePROTECT** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![CylancePROTECT в списке результатов](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в CylancePROTECT с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимы сведения о том, какой пользователь в CylancePROTECT соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CylancePROTECT.

Чтобы настроить и проверить единый вход Azure AD в CylancePROTECT, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя CylancePROTECT](#create-a-cylanceprotect-test-user)** требуется, чтобы в CylancePROTECT существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Из этого раздела вы узнаете, как включить единый вход Azure AD на портале Azure и настроить его в приложении CylancePROTECT.

**Чтобы настроить единый вход Azure AD в CylancePROTECT, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **CylancePROTECT** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_samlbase.png)

3. В разделе **доменов и URL-адресов приложения CylancePROTECT** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения CylancePROTECT](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес:
    
    | Регион | Значение URL-адреса |
    |----------|---------|
    | Северо-восток Азиатско-Тихоокеанского региона (APNE1)| ` https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Юго-восток Азиатско-Тихоокеанского региона (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Центральная Европа (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Северная Америка|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Северная Америка (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес:
    
    | Регион | Значение URL-адреса |
    |----------|---------|
    | Северо-восток Азиатско-Тихоокеанского региона (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Юго-восток Азиатско-Тихоокеанского региона (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Центральная Европа (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Северная Америка|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Северная Америка (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_400.png)

6. В разделе **CylancePROTECT Configuration** (Конфигурация CylancePROTECT) щелкните **Configure CylancePROTECT** (Настроить CylancePROTECT), чтобы открылось окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация CylancePROTECT](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_configure.png) 

7. Чтобы настроить единый вход на стороне **CylancePROTECT**, нужно отправить **скачанный сертификат в кодировке Base64, URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** администратору консоли. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-cylanceprotect-test-user"></a>Создание тестового пользователя CylancePROTECT

Из этого раздела вы узнаете, как создать пользователя Britta Simon в приложении CylancePROTECT. Чтобы добавить пользователей на платформе CylancePROTECT, обратитесь к администратору консоли. Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Из этого раздела вы узнаете, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к CylancePROTECT.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в CylancePROTECT, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **CylancePROTECT**.

    ![Ссылка на CylancePROTECT в списке приложений](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент CylancePROTECT на панели доступа, вы автоматически войдете в приложение CylancePROTECT.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_203.png


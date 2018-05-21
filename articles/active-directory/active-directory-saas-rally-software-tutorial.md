---
title: Учебник. Интеграция Azure Active Directory с Rally Software | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: f257ebe03129501034cca3b3e54e8fc796c357fc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Учебник. Интеграция Azure Active Directory с Rally Software

В этом учебнике описано, как интегрировать Rally Software с Azure Active Directory (Azure AD).

Интеграция Rally Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Rally Software.
- Вы можете включить автоматический вход пользователей в Rally Software (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Rally Software, вам потребуется следующее:

- подписка Azure AD;
- подписка Rally Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Rally Software из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-rally-software-from-the-gallery"></a>Добавление Rally Software из коллекции
Чтобы настроить интеграцию Rally Software с Azure AD, вам потребуется добавить Rally Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Rally Software из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Rally Software**, выберите **Rally Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Rally Software в списке результатов](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Rally Software для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Rally Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Rally Software.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Rally Software.

Чтобы настроить и проверить единый вход в Azure AD в Rally Software, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Rally Software](#create-a-rally-software-test-user)** требуется для создания дублирующего Britta Simon пользователя в Rally Software, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Rally Software.

**Чтобы настроить единый вход в Azure AD для Rally Software, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Rally Software** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. В разделе **Домены и URL-адреса Rally Software** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.rally.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Rally Software](https://help.rallydev.com/). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Rally Software** щелкните **Настроить Rally Software**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и идентификатор сущности SAML** из раздела **Краткий справочник**.

    ![Настройка Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Выполните вход в клиент **Rally Software** .

8. На панели инструментов вверху щелкните **Setup** (Настройка), затем выберите **Subscription** (Подписка).
   
    ![Подписка](./media/active-directory-saas-rally-software-tutorial/ic769531.png "Подписка")

9. Нажмите кнопку **Действие**. В правой верхней части панели инструментов выберите **Изменение подписки**.

10. На странице диалогового окна **Subscription** (Подписка) выполните указанные ниже действия, после чего нажмите кнопку **Save & Close** (Сохранить и закрыть).
   
    ![Аутентификация](./media/active-directory-saas-rally-software-tutorial/ic769542.png "Аутентификация")
   
    a. Из раскрывающегося списка "Аутентификация" выберите пункт **Rally or SSO authentication** (Аутентификация Rally или путем единого входа).

    Б. В текстовое поле **Identity Provider URL** (URL-адрес поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure. 

    c. В текстовое поле **SSO Logout** (Выход из службы единого входа) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-rally-software-test-user"></a>Создание тестового пользователя Rally Software

Чтобы пользователи Azure AD могли войти систему, их необходимо подготовить для приложения Rally Software, используя их имена пользователей Azure Active Directory.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход в клиент Rally Software.

2. Щелкните **Setup \> USERS** ("Настройка" > "ПОЛЬЗОВАТЕЛИ"), затем выберите **+ Add New** (+ Добавить).
   
    ![Пользователи](./media/active-directory-saas-rally-software-tutorial/ic781039.png "Пользователи")

3. Введите имя в текстовом поле "Новый пользователь" и щелкните **Добавить со сведениями**.

4. В разделе **Создание пользователя** выполните следующие действия.
   
    ![Создание пользователя](./media/active-directory-saas-rally-software-tutorial/ic781040.png "Создание пользователя")

    a. В текстовое поле **Имя пользователя** введите имя пользователя, например **Brittsimon**.
   
    Б. В текстовое поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    c. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    d. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    д. Щелкните **Save & Close** (Сохранить и закрыть).

   >[!NOTE]
   >Вы можете использовать любые другие инструменты создания учетных записей пользователя Rally Software или API, предоставляемые Rally Software для подготовки учетных записей пользователя Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Rally Software.

![Назначение роли пользователя][200] 

**Чтобы назначить Britta Simon для Rally Software, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Rally Software**.

    ![Ссылка на Rally Software в списке "Приложения"](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Rally Software на панели доступа, вы автоматически войдете в приложение Rally Software.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png


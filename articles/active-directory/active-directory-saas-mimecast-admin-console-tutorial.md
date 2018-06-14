---
title: Учебник. Интеграция Azure Active Directory с консолью администрирования Mimecast | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в консоль администрирования Mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: bef0312a3ee44a441f44eb2ae7e4292b966cec3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344751"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Учебник. Интеграция Azure Active Directory с консолью администрирования Mimecast

В этом руководстве описано, как интегрировать консоль администрирования Mimecast с Azure Active Directory (Azure AD).

Интеграция Azure AD с консолью администрирования Mimecast обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к консоли администрирования Mimecast.
- Вы можете включить автоматический вход пользователей в консоль администрирования Mimecast (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с консолью администрирования Mimecast, вам потребуется:

- подписка Azure AD;
- Подписка на консоль администрирования Mimecast с поддержкой единого входа

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление консоли администрирования Mimecast из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Добавление консоли администрирования Mimecast из коллекции
Чтобы настроить интеграцию консоли администрирования Mimecast с Azure AD, необходимо добавить консоль администрирования Mimecast из коллекции в список управляемых приложений SaaS.

**Чтобы добавить консоль администрирования Mimecast из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Консоль администрирования Mimecast**, выберите **Консоль администрирования Mimecast** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Консоль администрирования Mimecast в списке результатов](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в консоли администрирования Mimecast с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в консоли администрирования Mimecast соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в консоли администрирования Mimecast.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в консоли администрирования Mimecast.

Чтобы настроить и проверить единый вход Azure AD в консоли администрирования Mimecast, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя консоли администрирования Mimecast](#create-a-mimecast-admin-console-test-user)** требуется для того, чтобы в консоли администрирования Mimecast существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении консоли администрирования Mimecast.

**Чтобы настроить единый вход Azure AD в консоли администрирования Mimecast, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Консоль администрирования Mimecast** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. В разделе **Домены и URL-адреса консоли администрирования Mimecast** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа консоли администрирования Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > URL-адрес для входа зависит от региона.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация консоли администрирования Mimecast** щелкните **Настроить консоль администрирования Mimecast**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка консоли администрирования Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. В другом окне веб-браузера войдите в консоль администрирования Mimecast в качестве администратора.

8. Выберите **Services \> Application** (Службы > Приложение).

    ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "Services") (Службы)

9. Щелкните **Профили проверки подлинности**.

    ![Authentication Profiles](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "Authentication Profiles") (Профили аутентификации)
    
10. Щелкните **Новый профиль проверки подлинности**.

    ![New Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "New Authentication Profile") (Создать профиль аутентификации)

11. В разделе **Профиль проверки подлинности** сделайте следующее:

    ![Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "Authentication Profile") (Профиль аутентификации)
    
    a. В текстовом поле **Описание** введите имя конфигурации.
    
    Б. Выберите **Обязательное использование проверки подлинности SAML для консоли администрирования Mimecast**.
    
    c. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
    
    d. Вставьте **идентификатор сущности SAML**, скопированный на портале Azure, в текстовое поле **URL-адрес издателя**.
    
    д. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    f. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.
    
    >[!NOTE]
    >Значения URL-адреса входа в систему и URL-адреса выхода из системы для консоли администрирования Mimecast одинаковы.
    
    ж. Откройте в Блокноте скачанный на портале Azure сертификат в кодировке Base-64, удалите первую строку ("*--*") и последнюю строку ("*--*"), скопируйте остальное содержимое в буфер обмена и вставьте его в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).
    
    h. Установите флажок **Разрешить единый вход**.
    
    i. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985). 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Создание тестового пользователя консоли администрирования Mimecast

Чтобы разрешить пользователям Azure AD вход в консоль администрирования Mimecast, они должны быть подготовлены для консоли администрирования Mimecast. В случае с консолью администрирования Mimecast подготовка выполняется вручную.

* Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите в **консоль администрирования Mimecast** в качестве администратора.
2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
   ![Directories](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "Directories") (Каталоги)
3. Щелкните **Зарегистрировать новый домен**.
   
   ![Register New Domain](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "Register New Domain") (Зарегистрировать новый домен)
4. После создания нового домена щелкните **Новый адрес**.
   
   ![New Address](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "New Address") (Новый адрес)
5. В окне нового адреса выполните следующие действия.
   
   ![Сохранить](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "Сохранить")
   
   a. В соответствующие текстовые поля введите атрибуты **Email Address** (Адрес электронной почты), **Global Name** (Глобальное имя), **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) действительной учетной записи Azure AD, которую вы хотите подготовить.

   Б. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетных записей консоли администрирования Mimecast или API, предоставляемые консолью администрирования Mimecast для подготовки учетных записей пользователей Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к консоли администрирования Mimecast.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в консоли администрирования Mimecast, выполните следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Консоль администрирования Mimecast**.

    ![Ссылка на консоль администрирования Mimecast в списке "Приложения"](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Консоль администрирования Mimecast" на панели доступа, вы автоматически войдете в приложение консоли администрирования Mimecast.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png


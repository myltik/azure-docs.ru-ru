---
title: Руководство по интеграции Azure Active Directory с iQualify LMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 341f2175d283c08496534f3ac0d946dd8671f63c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Руководство по интеграции Azure Active Directory с iQualify LMS

В этом руководстве описано, как интегрировать приложение iQualify LMS с Azure Active Directory (Azure AD).

Интеграция iQualify LMS с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к iQualify LMS.
- Вы можете включить автоматический вход пользователей в iQualify LMS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с iQualify LMS, вам потребуется:

- подписка Azure AD;
- подписка iQualify LMS с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление iQualify LMS из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-iqualify-lms-from-the-gallery"></a>Добавление iQualify LMS из коллекции
Чтобы настроить интеграцию iQualify LMS с Azure AD, нужно добавить iQualify LMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить iQualify LMS из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **iQualify LMS**, выберите **iQualify LMS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![iQualify LMS в списке результатов](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в iQualify LMS с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в iQualify LMS соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в iQualify LMS.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в iQualify LMS.

Чтобы настроить и проверить единый вход Azure AD в iQualify LMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя iQualify LMS](#create-an-iqualify-lms-test-user)** требуется для того, чтобы в iQualify LMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении iQualify LMS.

**Чтобы настроить единый вход Azure AD в iQualify LMS, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **iQualify LMS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, то в разделе **Домены и URL-адреса приложения iQualify iLMS** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    | |
    |--|--|
    | Рабочая среда: `https://<yourorg>.iqualify.com/`|
    | Тестовая среда: `https://<yourorg>.iqualify.io`|
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 
    | |
    |--|--|
    | Рабочая среда: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Тестовая среда: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|--|
    | Рабочая среда: `https://<yourorg>.iqualify.com/login` |
    | Тестовая среда: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов iQualify LMS](https://www.iqualify.com). 

5. Приложение iQualify LMS ожидает, что утверждения SAML (язык разметки заявлений системы безопасности) будут отображаться в определенном формате. Настройте утверждения и установите значения атрибутов в разделе **Атрибуты пользователя** на странице интеграции с приложением iQualify, как показано на следующем снимке экрана.
    
    ![Настройка единого входа](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** выполните указанные ниже действия для каждой строки в следующей таблице.
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "ваш атрибут" | 

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Настройка единого входа](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    д. Повторите шаги от a до d для следующих строк таблицы. 

    > [!Note]
    > Повторять шаги от a до d для атрибута **person_id** **необязательно**.

7. В разделе **Сертификат подписи SAML** щелкните **Certificate (Base 64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. В разделе **Настройка iQualify LMS** щелкните **Настроить iQualify LMS**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Конфигурация iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Откройте новое окно браузера и войдите в среду iQualify в качестве администратора.

11. После входа щелкните свой аватар в правом верхнем углу, а затем — **Account settings** (Параметры учетной записи).

    ![Параметры учетной записи](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. В области параметров учетной записи щелкните меню ленты слева, а затем — **INTEGRATIONS** (Интеграции).
    
    ![Интеграции](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. В разделе INTEGRATIONS (Интеграции) щелкните значок **SAML**.

    ![Значок SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. В диалоговом окне **Параметры проверки подлинности SAML** сделайте следующее:

    ![Параметры проверки подлинности SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. В текстовом поле **SAML SINGLE SIGN-ON SERVICE URL** (URL-адрес службы единого входа SAML) введите значение **URL-адреса службы единого входа SAML** из окна настройки приложения Azure AD.
    
    Б. В текстовом поле **SAML LOGOUT URL** (URL-адрес выхода SAML) введите значение **Sign-Out URL** (URL-адрес выхода) из окна настройки приложения Azure AD.
    
    c. Откройте загруженный сертификат в блокноте, скопируйте его содержимое и вставьте его в текстовое поле **PUBLIC CERTIFICATE** (Общий сертификат).
    
    d. В поле **LOGIN BUTTON LABEL** (Подпись кнопки входа) укажите название кнопки, отображаемой на странице входа.
    
    д. Щелкните **СОХРАНИТЬ**.

    f. Нажмите кнопку **UPDATE** (Обновить).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Создание тестового пользователя iQualify LMS

В этом разделе вы создадите в iQualify пользователя с именем Britta Simon. Приложение iQualify LMS поддерживает JIT-подготовку пользователей. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в iQualify, он создается при попытке доступа к приложению iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к iQualify LMS.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в iQualify LMS, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **iQualify LMS**.

    ![Ссылка на iQualify LMS в списке "Приложения"](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Когда вы щелкните плитку iQualify LMS на панели доступа, должна появиться страница входа в приложение iQualify LMS. 

   ![Страница входа](./media/active-directory-saas-iqualify-tutorial/login.png) 

Нажмите кнопку **Sign in with Azure AD** (Вход с помощью Azure AD), чтобы выполнить автоматический вход в приложение iQualify LMS.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png


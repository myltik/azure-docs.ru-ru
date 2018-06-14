---
title: Руководство по интеграции Azure Active Directory с Pacific Timesheet | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Pacific Timesheet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e546e8ba-821a-4942-9545-c84b0670beab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 2f29c041c5464277937594a45b816fef395d04e6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345866"
---
# <a name="tutorial-azure-active-directory-integration-with-pacific-timesheet"></a>Руководство по интеграции Azure Active Directory с Pacific Timesheet

В этом руководстве описано, как интегрировать Pacific Timesheet с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Pacific Timesheet обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Pacific Timesheet.
- Вы можете включить автоматический вход пользователей в Pacific Timesheet (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Pacific Timesheet, вам потребуется:

- подписка Azure AD;
- подписка Pacific Timesheet с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Pacific Timesheet из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pacific-timesheet-from-the-gallery"></a>Добавление Pacific Timesheet из коллекции
Чтобы настроить интеграцию Pacific Timesheet с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pacific Timesheet из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Pacific Timesheet**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_search.png)

5. На панели результатов выберите **Pacific Timesheet** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Pacific Timesheet с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Pacific Timesheet соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pacific Timesheet.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pacific Timesheet.

Чтобы настроить и проверить единый вход Azure AD в Pacific Timesheet, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pacific Timesheet](#creating-a-pacific-timesheet-test-user)** требуется для создания пользователя Britta Simon в Pacific Timesheet, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pacific Timesheet.

**Чтобы настроить единый вход Azure AD в Pacific Timesheet, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Pacific Timesheet** выберите **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Pacific Timesheet** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Pacific Timesheet](http://www.pacifictimesheet.com/support).
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Pacific Timesheet** выберите **Настроить Pacific Timesheet** и откройте окно **Настройки единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_configure.png) 

7. Чтобы настроить единый вход на стороне **Pacific Timesheet**, нужно отправить скачанный **сертификат Base64**, **URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** в [службу поддержки Pacific Timesheet](http://www.pacifictimesheet.com/support). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-pacific-timesheet-test-user"></a>Создание тестового пользователя Pacific Timesheet

В этом разделе описано, как создать пользователя Britta Simon в приложении Pacific Timesheet. Чтобы создать пользователя в приложении, обратитесь в [службу поддержки Pacific Timesheet](http://www.pacifictimesheet.com/support).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Pacific Timesheet, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pacific Timesheet, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pacific Timesheet**.

    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Pacific Timesheet на панели доступа, вы автоматически войдете в приложение Pacific Timesheet.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_203.png


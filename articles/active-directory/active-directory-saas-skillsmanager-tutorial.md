---
title: Руководство по интеграции Azure Active Directory со Skills Manager | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Skills Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dab8debd-3b7b-4656-9bf0-1963ad8fce05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 610ef23dff752a1503b81e9b6a4ffb651e2b0cf5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346842"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-manager"></a>Руководство. Интеграция Azure Active Directory со Skills Manager

В этом руководстве описано, как интегрировать Skills Manager с Azure Active Directory (Azure AD).

Интеграция Azure AD с Skills Manager обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Skills Manager.
- Вы можете включить автоматический вход пользователей в Skills Manager (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Skills Manager, вам потребуется следующее:

- подписка Azure AD;
- подписка Skills Manager с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Skills Manager из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-skills-manager-from-the-gallery"></a>Добавление Skills Manager из коллекции
Чтобы настроить интеграцию Skills Manager с Azure AD, необходимо добавить Skills Manager из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Skills Manager из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Skills Manager** и выберите **Skills Manager** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Skills Manager в списке результатов](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Skills Manager с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Skills Manager соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Skills Manager.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Skills Manager.

Чтобы настроить и проверить единый вход Azure AD в Skills Manager, потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Skills Manager](#create-a-skills-manager-test-user)** требуется для того, чтобы в Skills Manager существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Skills Manager.

**Чтобы настроить единый вход Azure AD в Skills Manager, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Skills Manager** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Skills Manager** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Skills Manager](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://subdomain.skills-manager.com/kennametal`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://subdomain.skills-manager.com/public/SamlLogin2.aspx`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Для получения этих значений обратитесь в [службу поддержки Skills Manager](https://www.ibm.com/support/uk/?lnk=msu_uk).
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Skills Manager** щелкните **Настройка Skills Manager**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Skills Manager](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_configure.png) 

7. Чтобы настроить единый вход на стороне **Skills Manager**, нужно отправить скачанный **сертификат в кодировке Base64**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки Skills Manager](https://www.ibm.com/support/uk/?lnk=msu_uk). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-skills-manager-test-user"></a>Создание тестового пользователя Skills Manager

В этом разделе описано, как создать пользователя Britta Simon в приложении Skills Manager. Обратитесь в [службу поддержки Skills Manager](https://www.ibm.com/support/uk/?lnk=msu_uk), чтобы добавить пользователей на платформу Skills Manager. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Skills Manager.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Skills Manager, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Skills Manager**.

    ![Ссылка на Skills Manager в списке приложений](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Skills Manager на панели доступа, вы автоматически войдете в приложение Skills Manager.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_203.png


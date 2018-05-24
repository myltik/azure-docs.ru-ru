---
title: Руководство по интеграции Azure Active Directory с Optimizely | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 995b440cbf9dc93ec01fe0b86c69ea09cd4714ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Учебник. Интеграция Azure Active Directory с Optimizely

В этом учебнике описано, как интегрировать Optimizely с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Optimizely обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Optimizely.
- Вы можете включить автоматический вход пользователей в Optimizely (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Optimizely, вам потребуется:

- подписка Azure AD;
- подписка Optimizely с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Optimizely из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-optimizely-from-the-gallery"></a>Добавление Optimizely из коллекции
Чтобы настроить интеграцию Optimizely с Azure AD, необходимо добавить Optimizely из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Optimizely из коллекции, выполните следующие действия**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Optimizely**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. На панели результатов выберите **Optimizely** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Optimizely с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Optimizely соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Optimizely.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Optimizely.

Чтобы настроить и проверить единый вход Azure AD в Optimizely, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Optimizely](#creating-an-optimizely-test-user)** требуется для создания в Optimizely пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Optimizely.

**Чтобы настроить единый вход Azure AD в Optimizely, выполните следующие действия**

1. На портале Azure на странице интеграции с приложением **Optimizely** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. В разделе **Домен и URL-адреса приложения Optimizely** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://app.optimizely.net/<instance name>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Их необходимо заменить на фактический URL-адрес ответа и идентификатор, что описывается далее в этом учебнике. 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Optimizely** щелкните **Настроить Optimizely**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Чтобы настроить единый вход на стороне приложения **Optimizely**, обратитесь к менеджеру по технической поддержке Optimizely и предоставьте скачанный **сертификат (Base64)** и **URL-адрес службы единого входа SAML**. 

8. В ответ на ваше электронное сообщение Optimizely предоставит URL-адрес входа (для единого входа, инициируемого поставщиком услуг) и идентификатор (идентификатор сущности поставщика службы).

    a. Скопируйте **инициируемый SP URL-адрес единого входа**, предоставленный приложением Optimizely, и вставьте его в текстовое поле **URL-адрес входа** в разделе **Домен и URL-адреса приложения Optimizely** на портале Azure. 

    Б. Скопируйте **идентификатор сущности поставщика службы**, предоставленный приложением Optimizely, и вставьте его в текстовое поле **Идентификатор** в разделе **Домен и URL-адреса приложения Optimizely** на портале Azure. 

9. В отдельном окне браузера войдите в приложение Optimizely.

10. Щелкните имя своей учетной записи в верхнем правом углу, затем щелкните **Account Settings**(Параметры учетной записи).
   
    ![единого входа Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. На вкладке "Account" (Учетная запись) установите флажок **Enable SSO** (Включить единый вход) в области Single Sign On (Единый вход) раздела **Overview** (Общие сведения).
   
    ![единого входа Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Нажмите кнопку **Сохранить**

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-optimizely-test-user"></a>Создание тестового пользователя Optimizely

В этом разделе описано, как создать пользователя Britta Simon в приложении Optimizely.

1. На домашней странице выберите вкладку **Collaborators** (Сотрудники).

2. Чтобы добавить в проект нового сотрудника, щелкните **New Collaborator** (Создать сотрудника).
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Введите электронный адрес и назначьте роль. Нажмите кнопку **Пригласить**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Он получит приглашение по электронной почте. Этот электронный адрес будет использоваться для входа в приложение Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Optimizely.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Optimizely, выполните следующие действия**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Optimizely**.

    ![Настройка единого входа](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Optimizely" на панели доступа, вы автоматически войдете в приложение Optimizely. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png


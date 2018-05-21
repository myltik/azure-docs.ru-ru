---
title: Учебник. Интеграция Azure Active Directory с IdeaScale | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 29fecfb9a93934fd12492ee34ab619198c341c51
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Руководство. Интеграция Azure Active Directory с IdeaScale

В этом руководстве описано, как интегрировать IdeaScale с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением IdeaScale обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к IdeaScale.
- Вы можете включить автоматический вход пользователей в IdeaScale (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с IdeaScale, вам потребуется:

- подписка Azure AD;
- подписка IdeaScale с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление IdeaScale из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ideascale-from-the-gallery"></a>Добавление IdeaScale из коллекции
Чтобы настроить интеграцию IdeaScale с Azure AD, необходимо добавить IdeaScale из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IdeaScale из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **IdeaScale**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_search.png)

5. На панели результатов выберите **IdeaScale** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в IdeaScale с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в IdeaScale соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IdeaScale.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в IdeaScale.

Чтобы настроить и проверить единый вход Azure AD в IdeaScale, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя IdeaScale](#creating-an-ideascale-test-user)** требуется для создания в IdeaScale пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении IdeaScale.

**Чтобы настроить единый вход Azure AD в IdeaScale, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **IdeaScale** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. В разделе **Домены и URL-адреса приложения IdeaScale** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.ideascale.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов IdeaScale](http://support.ideascale.com/). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация IdeaScale** щелкните **Настроить IdeaScale**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и идентификатор сущности SAM** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт IdeaScale в качестве администратора.

8. Выберите **Параметры сообщества**.
   
    ![Параметры сообщества](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Параметры сообщества")

9. Последовательно выберите **Security \> Single Signon Settings** (Безопасность > Параметры единого входа).
   
    ![Параметры единого входа](./media/active-directory-saas-ideascale-tutorial/ic790848.png "Параметры единого входа")

10. Выберите для параметра **Single-Signon Type** (Тип единого входа) значение **SAML 2.0**.
   
    ![Тип единого входа](./media/active-directory-saas-ideascale-tutorial/ic790849.png "Тип единого входа")

11. В диалоговом окне **Параметры единого входа** выполните следующие действия.
   
    ![Параметры единого входа](./media/active-directory-saas-ideascale-tutorial/ic790850.png "Параметры единого входа")
   
    a. В текстовое поле **Идентификатор сущности IdP SAML** вставьте значение **идентификатора SAML**, скопированное на портале Azure.

    Б. Скопируйте содержимое скачанного на портале Azure файла метаданных и вставьте его в текстовое поле **Метаданные поставщика удостоверений SAML**.

    c. В текстовое поле **URL-адрес успешного выхода** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    d. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-ideascale-test-user"></a>Создание тестового пользователя IdeaScale

Чтобы пользователи Azure AD могли выполнять вход в IdeaScale, они должны быть подготовлены для IdeaScale. В случае с IdeaScale подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на корпоративном сайте службы **IdeaScale** в качестве администратора.

2. Выберите **Параметры сообщества**.
   
    ![Параметры сообщества](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Параметры сообщества")

3. Последовательно выберите **Basic Settings \> Member Management** (Основные параметры > Управление участниками).

4. Щелкните **Добавить участника**.
   
    ![Управление участниками](./media/active-directory-saas-ideascale-tutorial/ic790852.png "Управление участниками")

5. В разделе "Добавление участника" выполните следующие действия.
   
    ![Добавление новой записи](./media/active-directory-saas-ideascale-tutorial/ic790853.png "Добавление новой записи")
   
    a. В текстовое поле **Адреса электронной почты** введите адрес действующей учетной записи AAD, которую желаете подготовить.
   
    Б. Нажмите кнопку **Сохранить изменения**. 
   
    >[!NOTE]
    >Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
      
>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя IdeaScale или API, предоставляемые IdeaScale для подготовки учетных записей пользователя AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к IdeaScale.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении IdeaScale, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **IdeaScale**.

    ![Настройка единого входа](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа


Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент IdeaScale на панели доступа, вы автоматически войдете в приложение IdeaScale.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_203.png


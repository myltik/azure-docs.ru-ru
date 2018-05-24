---
title: Руководство по интеграции Azure Active Directory с etouches | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: e2b51227d89064e233ea1d1126bbf19169753094
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Руководство. Интеграция Azure Active Directory с etouches

В этом руководстве описано, как интегрировать приложение etouches с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением etouches обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к etouches.
- Вы можете включить автоматический вход пользователей в etouches (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с etouches, вам потребуется:

- подписка Azure AD;
- подписка etouches с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление etouches из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-etouches-from-the-gallery"></a>Добавление etouches из коллекции
Чтобы настроить интеграцию etouches с Azure AD, необходимо добавить etouches из коллекции в список управляемых приложений SaaS.

**Чтобы добавить etouches из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **etouches**, выберите **etouches** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![etouches в списке результатов](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в etouches с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в etouches соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в etouches.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в etouches.

Чтобы настроить и проверить единый вход Azure AD в etouches, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя etouches](#create-an-etouches-test-user)** требуется для создания в etouches пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении etouches.

**Чтобы настроить единый вход Azure AD в etouches, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **etouches** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. В разделе **Домены и URL-адреса приложения etouches** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Их необходимо заменить на фактический URL-адрес входа и идентификатор, как описано далее в этом руководстве.
    > 

4. Приложение etouches ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибут пользователя** приложения. На следующем снимке экрана приведен пример. 

    ![Атрибут пользователя](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Диалоговое окно добавления атрибута](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**. 

6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход для приложения, выполните следующие действия. 

    ![Настройка etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Войдите в приложение **etouches** с правами администратора.
   
    Б. Перейдите к настройке **SAML**.

    c. Перейдите в раздел **General Settings** (Общие параметры), откройте в Блокноте скачанный с портала Azure сертификат, скопируйте его содержимое и вставьте в текстовое поле IDP metadata (Метаданные поставщика удостоверений). 

    d. Нажмите кнопку **Save & Stay** (Сохранить и остаться).
  
    д. В разделе метаданных SAML нажмите кнопку **Update Metadata** (Обновить метаданные). 

    f. Это действие открывает страницу единого входа. Если единый вход работает, можно настроить имя пользователя.

    ж. В поле Username (Имя пользователя) выберите значение **emailaddress**, как показано на изображении ниже. 

    h. Скопируйте значение **SP entity ID** (Идентификатор сущности SP) и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения etouches** на портале Azure.

    i. Скопируйте значение **SSO URL / ACS** (URL-адрес и ASC для единого входа) и вставьте его в текстовое поле **URL-адрес для входа** в разделе **Домены и URL-адреса приложения etouches** на портале Azure.
   
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-etouches-test-user"></a>Создание тестового пользователя etouches

В этом разделе описано, как создать пользователя Britta Simon в приложении etouches. Обратитесь в [службу поддержки etouches](https://www.etouches.com/event-software/support/customer-support/), чтобы добавить пользователей на платформу etouches.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к etouches.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в etouches, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **etouches**.

    ![Ссылка на etouches в списке приложений](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа


Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку etouches на панели доступа, вы автоматически войдете в приложение etouches.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png


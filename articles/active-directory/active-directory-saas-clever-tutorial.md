---
title: Учебник. Интеграция Azure Active Directory с Clever | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: b7529b0942cd86b0d9e657d8d0f61313aa7f0a66
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140501"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Руководство. Интеграция Azure Active Directory с Clever

В этом учебнике описано, как интегрировать Clever с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Clever обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Clever.
- Вы можете включить автоматический вход пользователей в Clever (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Clever, вам потребуется:

- подписка Azure AD;
- подписка Clever с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Clever из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-clever-from-the-gallery"></a>Добавление Clever из коллекции
Чтобы настроить интеграцию Clever с Azure AD, необходимо добавить Clever из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Clever из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Clever**, выберите **Clever** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Clever в списке результатов](./media/active-directory-saas-clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Clever с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Clever соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Clever.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Clever.

Чтобы настроить и проверить единый вход Azure AD в Clever, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Clever](#create-a-clever-test-user)** требуется для того, чтобы в Clever существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Clever.

**Чтобы настроить единый вход Azure AD в Clever, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Clever** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-clever-tutorial/tutorial_clever_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Clever** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Clever](./media/active-directory-saas-clever-tutorial/tutorial_clever_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://clever.com/in/<companyname>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес `https://clever.com/oauth/saml/metadata.xml`.

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Clever](https://clever.com/about/contact/).

4. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.
    
    ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/tutorial_metadataurl.png)

5. Приложение Clever ожидает проверочные утверждения SAML в определенном формате, и поэтому вам нужно добавить в конфигурацию **атрибутов токена SAML** сопоставления настраиваемых атрибутов.

    На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/tutorial_clever_07.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута  | Значение атрибута |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | Firstname  | user.givenname |
    | Lastname  | user.surname |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым текстовое поле **Пространство имен**.
    
    d. Нажмите кнопку **ОК**.
    
7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-clever-tutorial/tutorial_general_400.png)

8. В другом окне веб-браузера войдите на корпоративный веб-сайт Clever в качестве администратора.

9. На панели инструментов выберите **Мгновенный вход**.

    ![Мгновенный вход](./media/active-directory-saas-clever-tutorial/ic798984.png "Мгновенный вход")

    > [!NOTE]
    > Перед проверкой единого входа следует обратиться к [группе поддержки клиентов Clever](https://clever.com/about/contact/), чтобы включить единый вход Office 365 в серверной части.

10. На странице **Мгновенный вход** выполните следующие действия.
    
      ![Мгновенный вход](./media/active-directory-saas-clever-tutorial/ic798985.png "Мгновенный вход")
    
      a. Введите **URL-адрес входа**.
    
      >[!NOTE]
      >**URL-адрес входа** является настраиваемым значением. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Clever](https://clever.com/about/contact/).
    
      Б. Для параметра **Identity System** (Система идентификации) выберите значение **ADFS**.

      c. В текстовое поле **URL-адрес метаданных** вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.
    
      d. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-clever-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-clever-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-clever-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-clever-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-clever-test-user"></a>Создание тестового пользователя Clever

Чтобы пользователи Azure AD могли выполнить вход в Clever, они должны быть подготовлены для Clever.

Обратитесь в [службу поддержки Clever](https://clever.com/about/contact/), чтобы добавить пользователей для платформы Clever. Перед использованием единого входа необходимо создать и активировать пользователей.

>[!NOTE]
>Вы можете использовать любые другие инструменты для создания учетной записи пользователя Clever или API, предоставляемые Clever для подготовки учетных записей Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Clever.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Clever, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Clever**.

    ![Ссылка на Clever в списке "Приложения"](./media/active-directory-saas-clever-tutorial/tutorial_clever_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Clever на панели доступа, вы автоматически войдете в приложение Clever.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clever-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clever-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clever-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clever-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clever-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clever-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clever-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clever-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clever-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с FloQast | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и FloQast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 013cb57d-567c-44d0-a119-e6ba6e607153
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: db254c7e0f200b447ef6d0b299080a7a542c235a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-floqast"></a>Руководство по интеграции Azure Active Directory с FloQast

В этом руководстве описано, как интегрировать FloQast с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением FloQast обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к FloQast.
- Вы можете включить автоматический вход пользователей в FloQast (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с FloQast, вам потребуется:

- подписка Azure AD;
- подписка FloQast с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление FloQast из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-floqast-from-the-gallery"></a>Добавление FloQast из коллекции
Чтобы настроить интеграцию FloQast с Azure AD, необходимо добавить FloQast из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FloQast из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **FloQast**, выберите **FloQast** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![FloQast в списке результатов](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в FloQast с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в FloQast соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FloQast.

Чтобы настроить и проверить единый вход Azure AD в FloQast, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FloQast](#create-a-floqast-test-user)** требуется для того, чтобы в FloQast существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении FloQast.

**Чтобы настроить единый вход Azure AD в FloQast, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **FloQast** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе**Домены и URL-адреса приложения FloQast** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения FloQast](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_url.png)

     В текстовом поле **Идентификатор** введите URL-адрес: `https://go.floqast.com/`

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения FloQast](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_url1.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://go.floqast.com/login/sso`.
     
5. Приложение FloQast ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка атрибута единого входа](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_attribute.png)
    
6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | FirstName           | user.givenname |
    | LastName        | user.surname |
    | Email       | user.mail    |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-floqast-tutorial/tutorial_attribute_04.png)

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-floqast-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере. Затем выполните следующие действия.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_certificate.png)

    a. Установите флажок **Показать дополнительные параметры подписывания сертификатов**.

    ![Утверждение сертификата](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_certificateassertion.png)

    Б. Для параметра **Вариант подписывания** выберите значение **Ответ знака SAML и утверждение**.

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-floqast-tutorial/tutorial_general_400.png)
    
9. Чтобы настроить единый вход на стороне **FloQast**, отправьте [группе поддержки FloQast](mailto:support@floqast.com) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-floqast-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-floqast-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-floqast-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-floqast-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-floqast-test-user"></a>Создание тестового пользователя FloQast

В этом разделе описано, как создать пользователя Britta Simon в FloQast. Обратитесь к [группе поддержки FloQast](mailto:support@floqast.com), чтобы добавить пользователей на платформу FloQast. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к FloQast.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в FloQast, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **FloQast**.

    ![Ссылка на FloQast в списке "Приложения"](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "FloQast" на панели доступа, вы автоматически войдете в приложение FloQast.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_203.png


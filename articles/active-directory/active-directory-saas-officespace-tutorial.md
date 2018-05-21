---
title: Руководство по интеграции Azure Active Directory с OfficeSpace Software | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в OfficeSpace Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 5f8698e50f9740d46a5cec708e3d5ae6bf1043ce
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Учебник. Интеграция Azure Active Directory с OfficeSpace Software

В этом руководстве описано, как интегрировать OfficeSpace Software с Azure Active Directory (Azure AD).

Интеграция OfficeSpace Software с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к OfficeSpace Software.
- Вы можете включить автоматический вход пользователей в OfficeSpace Software (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с OfficeSpace Software, вам потребуется следующее.

- подписка Azure AD;
- подписка OfficeSpace Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление OfficeSpace Software из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-officespace-software-from-the-gallery"></a>Добавление OfficeSpace Software из коллекции
Чтобы настроить интеграцию OfficeSpace Software с Azure AD, необходимо добавить OfficeSpace Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OfficeSpace Software из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **OfficeSpace Software**, выберите **OfficeSpace Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![OfficeSpace Software в списке результатов](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OfficeSpace Software с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в OfficeSpace Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OfficeSpace Software.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OfficeSpace Software.

Чтобы настроить и проверить единый вход в Azure AD в OfficeSpace Software, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя OfficeSpace Software](#create-a-officespace-software-test-user)** требуется для создания в OfficeSpace Software пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении OfficeSpace Software.

**Чтобы настроить единый вход Azure AD в OfficeSpace Software, выполните следующее.**

1. На портале Azure на странице интеграции с приложением **OfficeSpace Software** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_samlbase.png)

3. В разделе **Домены и URL-адреса приложения OfficeSpace Software** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения OfficeSpace Software](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Обратитесь в [службу поддержки клиентов OfficeSpace Software](mailto:support@officespacesoftware.com), чтобы получить эти значения. 

4. Приложение OfficeSpace Software ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка атрибута](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_attribute.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** выберите значение **user.mail** для параметра **Идентификатор пользователя**, а в каждой строке в таблице ниже выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка при добавлении ](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_04.png)

    ![Настройка атрибута](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.
 
6. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток** для сертификата.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. В разделе **OfficeSpace Software Configuration** (Конфигурация OfficeSpace Software) щелкните **Configure OfficeSpace Software** (Настройка OfficeSpace Software), чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Конфигурация OfficeSpace Software](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_configure.png) 

9. В другом окне веб-браузера войдите в свой клиент OfficeSpace Software в качестве администратора.

10. Выберите **Settings** (Параметры) и щелкните **Connectors** (Соединители).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Щелкните **SAML Authentication** (Аутентификация SAML).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. В разделе **Проверка подлинности SAML** сделайте следующее:

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. В текстовое поле **Logout provider url** (URL-адрес поставщика для выхода) вставьте значение **URL-адреса выхода**, скопированное с портала Azure.

    Б. В текстовое поле **Client idp target url** (Целевой URL-адрес IDP клиента) вставьте значение **URL-адреса службы единого входа SAML**, скопированное с портала Azure.

    c. В текстовое поле **Client IDP certificate fingerprint** (Отпечаток сертификата IDP клиента) вставьте значение **отпечатка**, скопированное с портала Azure. 

    d. Нажмите кнопку **Сохранить параметры**.


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-officespace-software-test-user"></a>Создание тестового пользователя OfficeSpace Software

Цель этого раздела — создать пользователя Britta Simon в OfficeSpace Software. Приложение OfficeSpace Software поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к OfficeSpace Software (если он еще не создан).

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться к [группе поддержки OfficeSpace Software](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к OfficeSpace Software.

![Назначение роли пользователя][200] 

**Чтобы назначить Britta Simon в OfficeSpace Software, выполните следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **OfficeSpace Software**.

    ![Ссылка на OfficeSpace Software в списке "Приложения"](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "OfficeSpace Software" на панели доступа, вы автоматически войдете в приложение OfficeSpace Software.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с BitaBIZ | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 031d7b11aea57b8bdd8b17e474db0c81b1bdbe76
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Руководство по интеграции Azure Active Directory с BitaBIZ

В этом руководстве описано, как интегрировать BitaBIZ с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BitaBIZ обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к BitaBIZ.
- Вы можете включить автоматический вход пользователей в BitaBIZ (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с BitaBIZ, вам потребуется:

- подписка Azure AD;
- подписка BitaBIZ с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление BitaBIZ из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-bitabiz-from-the-gallery"></a>Добавление BitaBIZ из коллекции.
Чтобы настроить интеграцию BitaBIZ с Azure AD, необходимо добавить BitaBIZ из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BitaBIZ из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **BitaBIZ**, выберите **BitaBIZ** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![BitaBIZ в списке результатов](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение BitaBIZ с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BitaBIZ соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BitaBIZ.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BitaBIZ.

Чтобы настроить и проверить единый вход Azure AD в BitaBIZ, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BitaBIZ](#create-a-bitabiz-test-user)** требуется для того, чтобы в BitaBIZ существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении BitaBIZ.

**Чтобы настроить единый вход Azure AD в BitaBIZ, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **BitaBIZ** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе**Домены и URL-адреса приложения BitaBIZ** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Значение в приведенном выше URL-адресе предназначено только для демонстрационных целей. Его необходимо заменить на фактический идентификатор, как описано далее в этом руководстве.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://www.bitabiz.com/dashboard`

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-bitabiz-tutorial/tutorial_general_400.png)
    
7. В разделе **Настройка BitaBIZ** щелкните **Настроить BitaBIZ**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. В другом окне веб-браузера войдите в свой клиент BitaBIZ с правами администратора.

9. Щелкните **SETUP ADMIN** (УСТАНОВИТЬ АДМИНИСТРАТОРА).

    ![Настройка BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

10. В разделе **Добавить значение** щелкните **Microsoft integrations** (Интеграции Майкрософт).

    ![Настройка BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings2.png)

11. Прокрутите вниз до раздела **Microsoft Azure AD (Enable single sign on)** (Microsoft Azure AD (Включить единый вход)) и сделайте следующее:

    ![Настройка BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings3.png)

    a. Скопируйте значение из текстового поля **Entity ID (”Identifier” in Azure AD)** (Идентификатор сущности ("Идентификатор" в Azure AD)) и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения BitaBIZ** на портале Azure. 
    
    Б. В текстовое поле **Azure AD Single Sign-On Service URL** (URL-адрес службы единого входа в Azure AD) вставьте **URL-адрес службы единого входа SAML**, скопированный на портале Azure.
    
    c. В текстовое поле **Azure AD SAML Entity ID** (Идентификатор сущности SAML Azure AD) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    d. Откройте скачанный файл **сертификата в кодировке Base64** в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Azure AD Signing Certificate (Base64 encoded)** (Сертификат для подписи Azure AD (в кодировке Base64)).

    д. Добавьте коммерческое имя домена электронной почты, то есть mycompany.com, в текстовое поле **Domain name** (Доменное имя), чтобы назначить единый вход для пользователей в организации с этим доменом электронной почты (необязательно).
    
    f. Установите флажок **SSO enabled** (единый вход включен) для учетной записи BitaBIZ.
    
    ж. Нажмите кнопку **Save Azure AD configuration** (Сохранить конфигурацию Azure AD), чтобы сохранить и активировать конфигурацию единого входа.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-bitabiz-test-user"></a>Создание тестового пользователя BitaBIZ

Чтобы пользователи Azure AD могли выполнять вход в BitaBIZ, они должны быть подготовлены в BitaBIZ.  
В случае с BitaBIZ подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт BitaBIZ компании в качестве администратора.

2. Щелкните **SETUP ADMIN** (УСТАНОВИТЬ АДМИНИСТРАТОРА).

    ![Добавление пользователя BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

3. Щелкните **Add users** (Добавить пользователей) в разделе **Organization** (Организация).

    ![Добавление пользователя BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/user1.png)

4. Нажмите кнопку **Add new employee** (Добавление нового сотрудника).

    ![Добавление пользователя BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/user2.png)

5. На диалоговой странице **Add new employee** (Добавление нового сотрудника) выполните следующие действия.

    ![Добавление пользователя BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/user3.png)

    a. В текстовом поле **First Name** (Имя) введите имя пользователя, например Britta.

    Б. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    c. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    d. Выберите дату в поле **Date of employment** (Дата приема на работу).

    д. Существуют другие необязательные атрибуты, которые можно настроить для пользователя. Дополнительные сведения о настройке сотрудника см. [здесь](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee).    
    
    f. Нажмите кнопку **Save employee** (Сохранить сотрудника).
    
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.
    
### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к BitaBIZ.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BitaBIZ, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **BitaBIZ**.

    ![Ссылка на BitaBIZ в списке приложений](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BitaBIZ на панели доступа, вы автоматически войдете в приложение BitaBIZ.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с Jamf Pro | Документы Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 488787adfbae0147c3bd0425d839e2ad8c5ed786
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308769"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Руководство. Интеграция Azure Active Directory с Jamf Pro

В этом руководстве описано, как интегрировать Jamf Pro с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Jamf Pro обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Jamf Pro.
- Вы можете включить автоматический вход пользователей в Jamf Pro (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Jamf Pro, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Jamf Pro.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Jamf Pro из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-jamf-pro-from-the-gallery"></a>Добавление Jamf Pro из коллекции
Чтобы настроить интеграцию Jamf Pro с Azure AD, нужно добавить Jamf Pro из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Jamf Pro из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Jamf Pro**, выберите **Jamf Pro** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Jamf Pro в списке результатов](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Jamf Pro с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Jamf Pro соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Jamf Pro.

Чтобы настроить и проверить единый вход Azure AD в Jamf Pro, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Jamf Pro](#create-a-jamf-pro-test-user)** требуется для того, чтобы в Jamf Pro существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Jamf Pro.

**Чтобы настроить единый вход Azure AD в Jamf Pro, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Jamf Pro** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе**Домены и URL-адреса приложения Jamf Pro** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<subdomain>.jamfcloud.com/saml/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.jamfcloud.com/saml/SSO`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Вы получите фактическое значение идентификатора из раздела **Единый вход** на портале Jamf Pro, что описано ниже. Вы можете извлечь фактическое значение **поддомена** из значения идентификатора и использовать эти сведения о **поддомене** в полях "URL-адрес для входа" и "URL-адрес ответа".

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. В другом окне веб-браузера войдите на сайт Jamf Pro своей организации в качестве администратора.

8. Щелкните **значок параметров** в правом верхнем углу страницы.

    ![Конфигурация Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Выберите **Единый вход**.

    ![Конфигурация Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Прокрутите вниз до элемента **IDENTITY PROVIDER** (Поставщик удостоверений) в разделе **Единый вход** и выполните следующие действия:

    ![Конфигурация Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. Выберите пункт **Другой** в раскрывающемся списке **IDENTITY PROVIDER** (Поставщик удостоверений).

    Б. В текстовом поле **OTHER PROVIDER** (Другой поставщик) введите **Azure AD**.

    c. Выберите элемент **URL-адрес метаданных** в раскрывающемся списке **IDENTITY PROVIDER METADATA SOURCE** (Источник метаданных поставщика удостоверений) и затем в следующем текстовом поле вставьте значение **URL-адреса метаданных федерации приложений**, скопированное на портале Azure.

    d. Скопируйте значение **Идентификатор сущности** и вставьте его в текстовое поле **Идентификатор (сущности)** в разделе **Домены и URL-адреса приложения Jamf Pro** на портале Azure.

    >[!NOTE]
    > Здесь `aadsso` является частью поддомена (которая приведена для справки). Используйте это значение в полях "URL-адрес для входа" и "URL-адрес ответа" в разделе **Домены и URL-адреса приложения Jamf Pro** на портале Azure.

    д. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-jamf-pro-test-user"></a>Создание тестового пользователя Jamf Pro

Чтобы пользователи Azure AD могли выполнять вход в Jamf Pro, они должны быть подготовлены в Jamf Pro. В случае с Jamf Pro подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт Jamf Pro организации в качестве администратора.

2. Щелкните **значок параметров** в правом верхнем углу страницы.

    ![Добавление сотрудника](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Щелкните **Jamf Pro User Accounts & Groups** (Группы и учетные записи пользователей Jamf Pro).

    ![Добавление сотрудника](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Нажмите кнопку **Создать**.

    ![Добавление сотрудника](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Выберите **Create Standard Account** (Создать стандартную учетную запись).

    ![Добавление сотрудника](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. В диалоговом окне **Новая учетная запись** сделайте следующее:

    ![Добавление сотрудника](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. В текстовом поле **USERNAME** (Имя пользователя) введите полное имя пользователя BrittaSimon.

    Б. Выберите соответствующие значения для параметров **ACCESS LEVEL** (Уровень доступа), **PRIVILEGE SET** (Набор привилегий) и **ACCESS STATUS** (Состояние доступа).
    
    c. В текстовом поле **FULL NAME** (Полное имя) введите полное имя пользователя Britta Simon.

    d. В текстовом поле **EMAIL ADDRESS** (Электронная почта) введите адрес электронной почты для учетной записи Britta Simon.

    д. В текстовое поле **PASSWORD** (Пароль) введите пароль пользователя.

    f. В текстовое поле **VERIFY PASSWORD** (Подтверждение пароля) введите пароль пользователя.

    ж. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Jamf Pro.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Jamf Pro, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Jamf Pro**.

    ![Ссылка на Jamf Pro в списке приложений](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Jamf Pro на панели доступа, вы автоматически войдете в приложение Jamf Pro.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png


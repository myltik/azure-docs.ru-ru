---
title: Руководство по интеграции Azure Active Directory с Cisco Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Cisco Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db1cea1d-ff0a-4f0d-b5fd-50ca32702d56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 6604c725a9c2594a2cefd79f2e0a7d0dd897e00e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34339838"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-cloud"></a>Руководство по интеграции Azure Active Directory с Cisco Cloud

В этом руководстве описано, как интегрировать Cisco Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с Cisco Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Cisco Cloud.
- Вы можете включить автоматический вход пользователей в Cisco Cloud (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Cisco Cloud, вам потребуется:

- подписка Azure AD;
- подписка на Cisco Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Cisco Cloud из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-cisco-cloud-from-the-gallery"></a>Добавление Cisco Cloud из коллекции.
Чтобы настроить интеграцию Cisco Cloud с Azure AD, необходимо добавить Cisco Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Cloud из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Cloud**, выберите **Cisco Cloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Cisco Cloud в списке результатов](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cisco Cloud с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Cisco Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cisco Cloud.

Чтобы настроить и проверить единый вход Azure AD в Cisco Cloud, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Cisco Cloud](#create-a-cisco-cloud-test-user)** нужно для того, чтобы в Cisco Cloud также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Cisco Cloud.

**Чтобы настроить единый вход Azure AD в Cisco Cloud, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Cisco Cloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Cisco Cloud** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Cisco Cloud](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `<subdomain>.cisco.com`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.cisco.com/sp/ACS.saml2`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Cisco Cloud](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://<subdomain>.cloudapps.cisco.com`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адресов ответа и входа. Чтобы получить их, обратитесь в [службу поддержки клиентов Cisco Cloud](mailto:cpr-ops@cisco.com).

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **Cisco Cloud**, отправьте скачанный **URL-адрес метаданных федерации приложений** в [группу поддержки Cisco Cloud](mailto:cpr-ops@cisco.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-ciscocloud-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-cisco-cloud-test-user"></a>Создание тестового пользователя Cisco Cloud

В этом разделе описано, как создать пользователя Britta Simon в Cisco Cloud. Обратитесь к [группе поддержки Cisco Cloud](mailto:cpr-ops@cisco.com) для добавления пользователей на платформу Cisco Cloud. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Cisco Cloud, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Cisco Cloud, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Cisco Cloud**.

    ![Ссылка на Cisco Cloud в списке "Приложения"](./media/active-directory-saas-ciscocloud-tutorial/tutorial_ciscocloud_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Cisco Cloud" на панели доступа, вы автоматически войдете в приложение Cisco Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ciscocloud-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с Adobe Captivate Prime | Документы Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: 1cb31fa9a5c7fdcd4158c9c2d2ddccc7125ba3bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337849"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Руководство по интеграции Azure Active Directory с Adobe Captivate Prime

В этом руководстве описано, как интегрировать приложение Adobe Captivate Prime с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Adobe Captivate Prime обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Adobe Captivate Prime.
- Вы можете включить автоматический вход пользователей в Adobe Captivate Prime (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Adobe Captivate Prime, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Adobe Captivate Prime.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Adobe Captivate Prime из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Добавление Adobe Captivate Prime из коллекции
Чтобы настроить интеграцию Adobe Captivate Prime с Azure AD, нужно добавить Adobe Captivate Prime из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Captivate Prime из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Adobe Captivate Prime**, выберите **Adobe Captivate Prime** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Adobe Captivate Prime в списке результатов](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Adobe Captivate Prime с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Adobe Captivate Prime соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Captivate Prime.

Чтобы настроить и проверить единый вход Azure AD в Adobe Captivate Prime, нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)** требуется для того, чтобы в Adobe Captivate Prime существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении Adobe Captivate Prime.

**Чтобы настроить единый вход Azure AD в Adobe Captivate Prime, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Adobe Captivate Prime** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Adobe Captivate Prime** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Adobe Captivate Prime](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://captivateprime.adobe.com`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес: `https://captivateprime.adobe.com/saml/SSO`

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Откройте вкладку **Свойства**, скопируйте **URL-адрес пользовательского доступа** и вставьте его в Блокнот.

    ![Ссылка для доступа пользователя](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Чтобы настроить единый вход на стороне **Adobe Captivate Prime**, нужно передать скачанный **XML-файл метаданных** и скопированный **URL-адрес пользовательского доступа** [группе поддержки Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Создание тестового пользователя Adobe Captivate Prime

В этом разделе описано, как создать пользователя Britta Simon в приложении Adobe Captivate Prime. Обратитесь в [группу поддержки Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com), чтобы добавить пользователей на платформу Adobe Captivate Prime. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Adobe Captivate Prime.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Adobe Captivate Prime, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Adobe Captivate Prime**.

    ![Ссылка на Adobe Captivate Prime в списке приложений](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Adobe Captivate Prime на панели доступа, вы автоматически войдете в приложение Adobe Captivate Prime.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_203.png


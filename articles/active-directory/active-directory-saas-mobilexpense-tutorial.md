---
title: Руководство по интеграции Azure Active Directory с Mobile Xpense | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Mobile Xpense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e649fc4e-3e15-4948-b977-00bfe9f7db13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: 95d4f97104df3a196aa3d0660cf5fca0ee3d89b8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34339780"
---
# <a name="tutorial-azure-active-directory-integration-with-mobile-xpense"></a>Руководство по интеграции Azure Active Directory с Mobile Xpense

В этом руководстве описано, как интегрировать Mobile Xpense с Azure Active Directory (Azure AD).

Интеграция Mobile Xpense с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Mobile Xpense.
- Вы можете включить автоматический вход пользователей в Mobile Xpense (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Mobile Xpense, вам потребуются:

- подписка Azure AD;
- подписка Mobile Xpense с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Mobile Xpense из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mobile-xpense-from-the-gallery"></a>Добавление Mobile Xpense из коллекции
Чтобы настроить интеграцию Mobile Xpense с Azure AD, необходимо добавить Mobile Xpense из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mobile Xpense из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Mobile Xpense**, выберите **Mobile Xpense** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Mobile Xpense в списке результатов](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure AD в Mobile Xpense с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход, Azure AD необходимо знать, какой пользователь в Mobile Xpense соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mobile Xpense.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mobile Xpense.

Чтобы настроить и проверить единый вход Azure AD в Mobile Xpense, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Mobile Xpense](#create-a-mobile-xpense-test-user)** требуется для того, чтобы в Mobile Xpense существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описывается, как включить единый вход Azure AD на портале Azure и настроить единый вход в приложении Mobile Xpense.

**Чтобы настроить единый вход Azure AD в Mobile Xpense, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Mobile Xpense** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом поставщиком удостоверений, в разделе **Домены и URL-адреса приложения Mobile Xpense** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Mobile Xpense](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url11.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://mobilexpense.com/ServiceProvider`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<sub-domain>.mobilexpense.com/NET/SSO/SAML20/SAML/AssertionConsumerService.aspx`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Mobile Xpense](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url22.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<sub-domain>.mobilexpense.com/<customername>`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Mobile Xpense](http://www.mobilexpense.net/contact). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **Mobile Xpense**, отправьте скачанный **XML-файл метаданных** [группе поддержки Mobile Xpense](http://www.mobilexpense.net/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-mobile-xpense-test-user"></a>Создание тестового пользователя Mobile Xpense

В этом разделе описано, как создать пользователя Britta Simon в MobileXpense. Чтобы добавить пользователей в MobileXpense, обратитесь в [службу поддержки клиентов MobileXpense](http://www.mobilexpense.net/contact). Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mobile Xpense.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mobile Xpense, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Mobile Xpense**.

    ![Ссылка на Mobile Xpense в списке "Приложения"](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Mobile Xpense" на панели доступа, вы автоматически войдете в приложение Mobile Xpense.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_203.png


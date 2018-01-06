---
title: "Учебник: Интеграция Azure Active Directory с Mobile Xpense | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Xpense мобильных устройств."
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
ms.openlocfilehash: 3beea4dc7889d84ba2724b9b4ebf88d2fae3a284
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mobile-xpense"></a>Учебник: Интеграция Azure Active Directory с Xpense мобильных устройств

В этом учебнике вы узнаете, как интегрировать Xpense мобильных устройств с Azure Active Directory (Azure AD).

Интеграция мобильных Xpense с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, кто имеет доступ к Mobile Xpense.
- Вы можете включить их учетные записи Azure AD пользователям автоматически получить вошедшего в Xpense Mobile (Single Sign-On).
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Настройка интеграции Azure AD с Xpense мобильных устройств, необходимо следующее:

- подписка Azure AD;
- Mobile Xpense единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление мобильных Xpense из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mobile-xpense-from-the-gallery"></a>Добавление мобильных Xpense из галереи
Для настройки интеграции Xpense мобильных устройств в Azure AD, необходимо добавить Mobile Xpense из коллекции в список управляемых приложений SaaS.

**Добавление мобильных Xpense из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Mobile Xpense**выберите **Mobile Xpense** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Xpense мобильных устройств в списке результатов](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с Xpense Mobile основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в Xpense мобильных устройств для пользователя в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в Xpense мобильных устройств необходимо установить.

В Mobile Xpense, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с Xpense мобильных устройств, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя мобильных Xpense](#create-a-mobile-xpense-test-user)**  — на аналог Саймон Britta в Xpense Mobile, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении Mobile Xpense.

**Чтобы настроить Azure AD единого входа с Xpense мобильных устройств, выполните следующие действия.**

1. На портале Azure на **Mobile Xpense** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_samlbase.png)

3. На **URL-адреса и домена Xpense Mobile** статьи, выполните следующие действия, если вы хотите настроить приложение в режиме инициированный IDP:

    ![URL-адреса и домена Xpense Mobile единого входа сведения](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url11.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://mobilexpense.com/ServiceProvider`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<sub-domain>.mobilexpense.com/NET/SSO/SAML20/SAML/AssertionConsumerService.aspx`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![URL-адреса и домена Xpense Mobile единого входа сведения](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url22.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<sub-domain>.mobilexpense.com/<customername>`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Обратитесь к [группа поддержки клиента мобильного Xpense](http://www.mobilexpense.net/contact) для получения этих значений. 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_400.png)

7. Для настройки единого входа на **Mobile Xpense** стороны, необходимо отправить загруженного **метаданные в формате XML** для [Mobile Xpense поддержки](http://www.mobilexpense.net/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

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
 
### <a name="create-a-mobile-xpense-test-user"></a>Создание тестового пользователя Xpense мобильных устройств

В этом разделе описано, как создать пользователя Britta Simon в MobileXpense. Чтобы добавить пользователей в MobileXpense, обратитесь в [службу поддержки клиентов MobileXpense](http://www.mobilexpense.net/contact). Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к Mobile Xpense.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Xpense мобильных устройств, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Mobile Xpense**.

    ![Ссылка Xpense мобильных устройств в списке приложений](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки Mobile Xpense плитки на панели доступа, вы должны получить автоматически вошедшего в приложение Mobile Xpense.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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


---
title: Руководство по интеграции Azure Active Directory с Vodeclic | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Vodeclic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: bc889919f2d869478843881cc8eae06fc9cb232c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Руководство по интеграции Azure Active Directory с Vodeclic

Из этого руководства вы узнаете, как интегрировать Vodeclic с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Vodeclic обеспечивает следующие преимущества:

- c помощью Azure AD вы можете контролировать доступ к Vodeclic;
- вы можете включить автоматический вход пользователей в Vodeclic (единый вход) с использованием учетной записи Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Vodeclic, вам потребуется:

- подписка Azure AD;
- подписка Vodeclic с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Vodeclic из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="add-vodeclic-from-the-gallery"></a>Добавление Vodeclic из коллекции
Чтобы настроить интеграцию Vodeclic с Azure AD, необходимо добавить Vodeclic из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Vodeclic из коллекции, сделайте следующее:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Vodeclic**. Чтобы добавить приложение, на панели результатов выберите **Vodeclic** и нажмите кнопку **Добавить**.

    ![Vodeclic в списке результатов](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Из этого раздела вы узнаете, как настроить и проверить единый вход Azure AD в Vodeclic с помощью тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимы сведения о том, какой пользователь в Vodeclic соответствует пользователю в Azure AD. Другими словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Vodeclic.

Значение **имени пользователя** в Vodeclic должно соответствовать **имени пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями.

Чтобы настроить и проверить единый вход Azure AD в Vodeclic, выполните инструкции в следующих стандартных блоках:

1. [Настройка единого входа в Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли пользоваться этой функцией.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Vodeclic](#create-a-vodeclic-test-user) требуется, чтобы в приложении существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) позволяет убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Из этого раздела вы узнаете, как включить единый вход Azure AD на портале Azure и настроить его в приложении Vodeclic.

**Чтобы настроить единый вход Azure AD в Vodeclic, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Vodeclic** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим единого входа** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Чтобы настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Vodeclic** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml`.

    Б. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Чтобы настроить приложение в режиме, инициируемом **поставщиком услуг**, установите флажок **Показать дополнительные параметры URL-адресов** и сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Vodeclic](mailto:hotline@vodeclic.com).

5. В разделе **Сертификат подписи SAML** выберите **XML метаданных**. Затем сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Чтобы настроить единый вход на стороне **Vodeclic**, отправьте в [службу поддержки Vodeclic](mailto:hotline@vodeclic.com) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-vodeclic-test-user"></a>Создание тестового пользователя Vodeclic

Из этого раздела вы узнаете, как создать пользователя Britta Simon в приложении Vodeclic. Обратитесь в [службу поддержки Vodeclic](mailto:hotline@vodeclic.com), чтобы добавить пользователей на платформу Vodeclic. Перед использованием единого входа необходимо создать и активировать пользователей.

> [!NOTE]
> Согласно требованиям приложения вам, возможно, понадобится включить свой компьютер в список разрешенных устройств. Для этого отправьте свой общедоступный IP-адрес в [службу поддержки Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Из этого раздела вы узнаете, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к приложению Vodeclic.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Vodeclic, сделайте следующее:**

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Затем перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Vodeclic**.

    ![Ссылка на Vodeclic в списке приложений](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку Vodeclic на панели доступа, вы автоматически войдете в приложение Vodeclic.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png


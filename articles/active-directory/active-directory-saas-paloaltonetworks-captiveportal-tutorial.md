---
title: Руководство по интеграции Azure Active Directory с Palo Alto Networks (Captive Portal) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (Captive Portal).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aaa2adc30d1d798312b50370d4b635d0d8a123e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Руководство по интеграции Azure Active Directory с Palo Alto Networks (Captive Portal)

В этом руководстве описано, как интегрировать Palo Alto Networks (Captive Portal) с Azure Active Directory (Azure AD).

Интеграция Azure AD с Palo Alto Networks (Captive Portal) обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks (Captive Portal).
- Вы можете включить автоматический вход пользователей в Palo Alto Networks (Captive Portal) (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Palo Alto Networks (Captive Portal), вам потребуется:

- подписка Azure AD;
- подписка Palo Alto Networks (Captive Portal) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Palo Alto Networks (Captive Portal) из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Добавление Palo Alto Networks (Captive Portal) из коллекции
Чтобы настроить интеграцию Palo Alto Networks (Captive Portal) с Azure AD, необходимо добавить Palo Alto Networks (Captive Portal) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks (Captive Portal) из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Palo Alto Networks (Captive Portal)** и выберите **Palo Alto Networks (Captive Portal)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Palo Alto Networks (Captive Portal) в списке результатов](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks (Captive Portal) с использованием тестового пользователя Britta Simon.

Чтобы активировать единый вход, Azure AD необходима информация о том, какой пользователь в Palo Alto Networks (Captive Portal) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Palo Alto Networks (Captive Portal).

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Palo Alto Networks (Captive Portal).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (Captive Portal), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Palo Alto Networks (Captive Portal)](#create-a-palo-alto-networks---captive-portal-test-user)** требуется для того, чтобы в Palo Alto Networks (Captive Portal) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Palo Alto Networks (Captive Portal).

**Чтобы настроить единый вход Azure AD в Palo Alto Networks (Captive Portal), сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Palo Alto Networks (Captive Portal)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Palo Alto Networks (Captive Portal)** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks (Captive Portal)](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<Customer Firewall Hostname>/SAML20/SP`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support).

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

6. В другом окне браузера откройте сайт Palo Alto с правами администратора.

7. Щелкните **Device** (Устройство).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Чтобы импортировать файл метаданных, в левой области навигации выберите **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Сделайте следующее в окне Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Укажите имя в текстовом поле **Profile Name** (Имя профиля), например "Пользовательский интерфейс администратора Azure AD".
    
    Б. В разделе **Identity Provider Metadata** (Метаданные поставщика удостоверений) щелкните **Browse** (Обзор) и выберите файл metadata.xml, загруженный ранее с портала Azure.
    
    c. Щелкните **ОК**

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Создание тестового пользователя в Palo Alto Networks (Captive Portal)

Цель этого раздела — создать в Palo Alto Networks (Captive Portal) тестового пользователя с именем Britta Simon. Palo Alto Networks (Captive Portal) поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не создан, то он будет создан при попытке получить доступ к Palo Alto Networks (Captive Portal). 

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в [службу поддержки Palo Alto Networks (Captive Portal)](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks (Captive Portal), чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Palo Alto Networks (Captive Portal), выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Palo Alto Networks (Captive Portal)**.

    ![Ссылка на Palo Alto Networks (Captive Portal) в списке приложений](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

На виртуальной машине Windows портал авторизации Captive Portal настроен за брандмауэром.  Для тестирования единого входа на портал Captive Portal войдите на виртуальную машину Windows с помощью RDP. Откройте в браузере любой веб-сайт в рамках сеанса RDP. Браузер должен автоматически открыть URL-адрес единого входа и запросить проверку подлинности. После завершения проверки подлинности должна появиться возможность перехода на веб-сайты. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png


---
title: Руководство по интеграции Azure Active Directory с KnowBe4 Security Awareness Training | Документация Майкрософт
description: Узнайте, как настраивать единый вход между Azure Active Directory и KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: cfbcd48b1cb49f2e042ab0d4afa71d1d20226a46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Руководство по интеграции Azure Active Directory с KnowBe4 Security Awareness Training

В этом руководстве вы узнаете, как интегрировать KnowBe4 Security Awareness Training с Azure Active Directory (Azure AD).

Интеграция KnowBe4 Security Awareness Training с Azure AD предоставляет следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к KnowBe4 Security Awareness Training.
- Вы можете позволить пользователям автоматически входить в KnowBe4 Security Awareness Training (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с KnowBe4 Security Awareness Training, вам потребуется:

- подписка Azure AD;
- подписка KnowBe4 Security Awareness Training с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление KnowBe4 Security Awareness Training из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Добавление KnowBe4 Security Awareness Training из коллекции
Для настройки интеграции KnowBe4 Security Awareness Training с Azure AD необходимо добавить KnowBe4 Security Awareness Training из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowBe4 Security Awareness Training из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **KnowBe4 Security Awareness Training**, на панели результатов выберите **KnowBe4 Security Awareness Training** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![KnowBe4 Security Awareness Training в списке результатов](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в KnowBe4 Security Awareness Training с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь KnowBe4 Security Awareness Training соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в KnowBe4 Security Awareness Training.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в KnowBe4 Security Awareness Training.

Чтобы настроить и проверить единый вход Azure AD в KnowBe4 Security Awareness Training, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя KnowBe4 Security Awareness Training](#create-a-knowbe4-security-awareness-training-test-user)** требуется для создания пользователя Britta Simon в KnowBe4 Security Awareness Training, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении KnowBe4 Security Awareness Training.

**Чтобы настроить единый вход Azure AD в KnowBe4 Security Awareness Training, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **KnowBe4 Security Awareness Training** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

3. В разделе **KnowBe4 Security Awareness Training** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения KnowBe4 Security Awareness Training](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь в [службу поддержки клиентов KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com), чтобы получить это значение. 

    Б. В текстовом поле **Идентификатор** введите строковое значение `KnowBe4`.

    > [!NOTE]
    >Это значение следует вводить с учетом регистра.

4. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-knowbe4-tutorial/tutorial_general_400.png)

6. В разделе **Настройка KnowBe4 Security Awareness Training** щелкните **Настроить KnowBe4 Security Awareness Training**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка KnowBe4 Security Awareness Training](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_configure.png) 

7. Чтобы настроить единый вход на стороне **KnowBe4 Security Awareness Training**, необходимо отправить скачанный **необработанный файл сертификата**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа** в [службу поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Создание тестового пользователя KnowBe4 Security Awareness Training

В этом разделе описано, как создать пользователя с именем Britta Simon в приложении KnowBe4 Security Awareness Training. Приложение KnowBe4 Security Awareness Training поддерживает JIT-подготовку, включенную по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь будет создан при попытке получить доступ к приложению KnowBe4 Security Awareness Training (если он еще не создан). 

>[!NOTE]
>Если необходимо создать пользователя вручную, свяжитесь со [службой поддержки KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к KnowBe4 Security Awareness Training, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в KnowBe4 Security Awareness Training, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **KnowBe4 Security Awareness Training**.

    ![Ссылка на KnowBe4 Security Awareness Training в списке приложений](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
  
Щелкнув панель KnowBe4 Security Awareness Training на панели доступа, вы автоматически войдете в приложение KnowBe4 Security Awareness Training. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_203.png


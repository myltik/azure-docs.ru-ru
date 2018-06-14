---
title: Руководство. Интеграция Azure Active Directory с Dynamic Signal | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Dynamic Signal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 863f7340-b065-4f59-b092-daa67da6f703
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 759d5656873d7870dd0c46c30e89372878b300f8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344853"
---
# <a name="tutorial-azure-active-directory-integration-with-dynamic-signal"></a>Руководство. Интеграция Azure Active Directory с Dynamic Signal

В этом руководстве описано, как интегрировать Dynamic Signal с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Dynamic Signal обеспечивает следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к Dynamic Signal.
- Вы можете включить автоматический вход пользователей в Dynamic Signal (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Dynamic Signal, вам потребуется:

- подписка Azure AD;
- подписка на Dynamic Signal с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Dynamic Signal из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-dynamic-signal-from-the-gallery"></a>Добавление Dynamic Signal из коллекции.
Чтобы настроить интеграцию Dynamic Signal с Azure AD, необходимо добавить приложение Dynamic Signal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Dynamic Signal из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Dynamic Signal**, выберите **Dynamic Signal** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Dynamic Signal в списке результатов](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Dynamic Signal с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Dynamic Signal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Dynamic Signal.

Чтобы настроить и проверить единый вход Azure AD в Dynamic Signal, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Dynamic Signal](#create-a-dynamic-signal-test-user)** требуется для того, чтобы в Dynamic Signal существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Dynamic Signal.

**Чтобы настроить единый вход Azure AD в Dynamic Signal, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Dynamic Signal** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Dynamic Signal** выполните следующие действия:
 
    ![Сведения о домене и URL-адресах единого входа приложения Dynamic Signal](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.voicestorm.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.voicestorm.com`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.voicestorm.com/User/SsoResponse`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Dynamic Signal](mailto:support@dynamicsignal.com). 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_400.png)
    
6. В разделе **Настройка Dynamic Signal** щелкните **Настроить Dynamic Signal**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Dynamic Signal](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_configure.png) 

7. Чтобы настроить единый вход на стороне **Dynamic Signal**, нужно отправить скачанный **сертификат (Base64), URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки Dynamic Signal](mailto:support@dynamicsignal.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-dynamic-signal-test-user"></a>Создание тестового пользователя Dynamic Signal

Цель этого раздела — создать пользователя с именем Britta Simon в Dynamic Signal. Приложение Dynamic Signal поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Dynamic Signal (если он еще не создан).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Dynamic Signal](mailto:support@dynamicsignal.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Dynamic Signal.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Dynamic Signal, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Dynamic Signal**.

    ![Ссылка на Dynamic Signal в списке приложений](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Dynamic Signal на панели доступа, вы автоматически войдете в приложение Dynamic Signal.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_203.png


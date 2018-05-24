---
title: Учебник. Интеграция Azure Active Directory с Clarizen | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 8af9e7023142077fdeed0262e5f39921b158a14f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Руководство. Интеграция Azure Active Directory с Clarizen

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Clarizen. Эта интеграция дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Clarizen.
- Вы можете включить автоматический вход пользователей в Clarizen (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Сценарий, описанный в этом руководстве, состоит из двух основных задач.

1. Добавление Clarizen из коллекции.
2. Настройка и проверка единого входа в Azure AD.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям
Чтобы настроить интеграцию Azure AD с Clarizen, вам потребуется:

- подписка Azure AD;
- подписка Clarizen с поддержкой единого входа.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Проверьте единый вход Azure AD в тестовой среде. Не используйте рабочую среду без необходимости.
- Если у вас нет тестовой среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Добавление Clarizen из коллекции
Чтобы настроить интеграцию Clarizen с Azure AD, добавьте Clarizen из коллекции в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**.

    ![Значок Azure Active Directory][1]

2. Щелкните **Корпоративные приложения**. Затем щелкните **Все приложения**.

    ![Выбор пунктов "Корпоративные приложения" и "Все приложения".][2]

3. В верхней части диалогового окна нажмите кнопку **Добавить**.

    ![Кнопка "Добавить"][3]

4. В поле поиска введите **Clarizen**.

    ![Ввод Clarizen в поле поиска](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. В области результатов выберите **Clarizen** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Выбор Clarizen в области результатов](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В следующих разделах описана настройка и проверка единого входа Azure AD в Clarizen с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Clarizen соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Clarizen. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Clarizen.

Чтобы настроить и проверить единый вход Azure AD в Clarizen, выполните инструкции ниже.

1. **[Настройте единый вход в Azure AD](#configure-azure-ad-single-sign-on)**, чтобы пользователи могли использовать эту функцию.
2. **[Создайте тестового пользователя Azure AD](#create-an-azure-ad-test-user)** для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создайте тестового пользователя Clarizen](#create-a-clarizen-test-user)**, чтобы в Clarizen был тестовый пользователь Britta Simon, связанный с соответствующим представлением в Azure AD.
4. **[Назначьте тестового пользователя Azure AD](#assign-the-azure-ad-test-user)**, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверьте единый вход](#test-single-sign-on)**, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Включите единый вход Azure AD на портале Azure и настройте его в приложении Clarizen.

1. На портале Azure на странице интеграции с приложением **Clarizen** щелкните **Единый вход**.

    ![Выбор пункта "Единый вход"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Выбор параметра "Единый вход на основе SAML"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. В разделе **Домены и URL-адреса приложения Clarizen** сделайте следующее.

    ![Поля идентификатора и URL-адреса ответа](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. В поле **Идентификатор** введите значение **Clarizen**.

    Б. В поле **URL-адрес ответа** введите URL-адрес, используя следующий шаблон: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Значения, указанные выше, приведены в качестве примера. Используйте фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить фактические значения, обратитесь в [техническую поддержку Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Нажатие кнопки "Создать новый сертификат"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите дату окончания срока действия. Нажмите кнопку **Сохранить**.

    ![Выбор и сохранение даты окончания действия](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** поставьте флажок **Сделать новый сертификат активным** и нажмите кнопку **Сохранить**.

    ![Установка флажка для активации нового сертификата](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. В диалоговом окне **Сертификат возобновления** нажмите кнопку **ОК**.

    ![Нажатие кнопки "ОК" для подтверждения активации сертификата](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Нажатие кнопки "Сертификат (Base64)" для начала загрузки](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. В разделе **Конфигурация Clarizen** нажмите кнопку **Настроить Clarizen**, чтобы открыть окно **Настройка единого входа**.

    ![Нажатие кнопки "Настроить Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![Окно "Настройка единого входа" с файлами и URL-адресами](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. В другом окне веб-браузера войдите на корпоративный сайт Clarizen в качестве администратора.

11. Щелкните свое имя пользователя и выберите пункт **Settings** (Параметры).

    ![Выбор пункта "Параметры" по именем пользователя](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "Параметры")

12. Откройте вкладку **Global Settings** (Глобальные параметры). Рядом с параметром **Federated Authentication** (Федеративная проверка подлинности) нажмите кнопку **edit** (изменить).

    ![Вкладка "Глобальные параметры"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "Глобальные параметры")

13. В диалоговом окне **Federated Authentication** (Федеративная проверка подлинности) выполните следующие действия.

    ![Диалоговое окно "Федеративная проверка подлинности"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "Федеративная проверка подлинности")

    a. Выберите **Enable Federated Authentication** (Включить федеративную проверку подлинности).

    Б. Чтобы отправить загруженный сертификат, нажмите кнопку **Отправить** .

    c. В текстовом поле **Sign-in URL** (URL-адрес входа) введите значение **URL-адреса службы единого входа SAML** из окна настройки приложения Azure AD.

    d. В текстовом поле **Sign-out URL** (URL-адрес выхода) введите значение **URL-адреса выхода** из окна настройки приложения Azure AD.

    д. Установите флаг **Использовать POST**.

    f. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
На портале Azure создайте тестового пользователя с именем Britta Simon.

![Имя и адрес электронной почты тестового пользователя Azure AD][100]

1. На портале Azure в области слева щелкните значок **Azure Active Directory**.

    ![Значок Azure Active Directory](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Откройте раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.

    ![Выбор разделов "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.

    ![Кнопка "Добавить"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь" с заполненными полями "Имя", "Адрес электронной почты" и "Пароль"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-clarizen-test-user"></a>Создание тестового пользователя Clarizen
Чтобы пользователи Azure AD могли входить в приложение Clarizen, необходимо подготовить учетные записи пользователей. В случае с Clarizen подготовка выполняется вручную.

1. Войдите на корпоративный сайт Clarizen в качестве администратора.

2. Выберите параметр **Пользователи**.

    ![Выберите пункт People (Пользователи)](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "Пользователи")

3. Нажмите кнопку **Пригласить пользователя**.

    ![Кнопка Invite User](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "Пригласить пользователя")

4. В диалоговом окне **Invite People** (Приглашение пользователей) выполните следующие действия.

    ![Диалоговое окно Invite People](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "Приглашение пользователей")

    a. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты учетной записи Britta Simon.

    Б. Нажмите кнопку **Пригласить**.

    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Разрешите пользователю Britta Simon применять единый вход Azure, предоставив доступ к Clarizen.

![Назначение тестового пользователя][200]

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Выбор пунктов "Корпоративные приложения" и "Все приложения".][201]

2. В списке приложений выберите **Clarizen**.

    ![Выбор Clarizen в списке](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. В области слева выберите **Пользователи и группы**.

    ![Выбор пункта "Пользователи и группы"][202]

4. Нажмите кнопку **Add** (Добавить). Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Нажатие кнопки "Добавить" и диалоговое окно "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа
Проверьте конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Clarizen на панели доступа, вы автоматически войдете в приложение Clarizen.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png

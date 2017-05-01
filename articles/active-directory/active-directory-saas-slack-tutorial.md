---
title: "Руководство по интеграции Azure Active Directory с приложением Slack | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: a1d7c2f01d3bf345f63077c4d42d79476235e941
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Учебник. Интеграция Azure Active Directory с Slack

В этом руководстве описано, как интегрировать Slack с Azure Active Directory (Azure AD).

Интеграция Slack с Azure AD дает следующие возможности.

* Контроль доступа к Slack через Azure AD.
* Возможность автоматического входа пользователей в Slack с учетными записями Azure AD благодаря поддержке единого входа.
* Централизованное управление учетными записями через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Slack, вам потребуется:

* подписка Azure AD;
* Подписка Slack с поддержкой SSO

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий, описанных в этом руководстве.
>
>

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

* Используйте рабочую среду только при необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Предлагаемый сценарий состоит из двух основных компонентов.

* Добавление Slack из коллекции
* Настройка и проверка единого входа Azure AD.

## <a name="add-slack-from-the-gallery"></a>Добавление Slack из коллекции
Чтобы настроить интеграцию Slack с Azure AD, добавьте Slack из коллекции в список управляемых приложений SaaS, выполнив следующие действия.

1. Откройте [портал Azure](https://portal.azure.com).
2. В области слева щелкните кнопку **Azure Active Directory**.

    ![Кнопка Azure Active Directory][1]

3. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Кнопка "Все приложения" в колонке "Корпоративные приложения"][2]

4. В верхней части диалогового окна **Все приложения** нажмите кнопку **Добавить**.

    ![Кнопка "Добавить" в диалоговом окне "Все приложения"][3]

5. В поле поиска введите **Slack**.

    ![Поле поиска "Добавить приложение"](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. На панели результатов выберите **Slack** и нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Выбор Slack в области результатов](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Slack с использованием тестового пользователя Britta Simon.

Чтобы использовать единый вход, в Azure AD необходимо установить связь между пользователем Azure AD и его представлением в Slack. Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **Username** (Имя пользователя) в Slack.

Чтобы настроить и проверить единый вход Azure AD в Slack, выполните действия в следующих стандартных блоках:

1. [Настройте единый вход Azure AD](#configuring-azure-ad-single-sign-on), чтобы пользователи могли использовать эту функцию.
2. [Создайте тестового пользователя Azure AD](#creating-an-azure-ad-test-user) для проверки работы единого входа Azure AD для пользователя Britta Simon.
3. [Создайте тестового пользователя Slack](#creating-a-slack-test-user), чтобы связать с ним пользователя Britta Simon в Azure AD.
4. [Назначьте тестового пользователя Azure AD](#assigning-the-azure-ad-test-user), чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. [Проверьте единый вход](#testing-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе вы включите единый вход Azure AD на портале Azure и настроите единый вход в приложении Slack. Для этого выполните следующее.

1. На портале Azure на странице интеграции с приложением **Slack** щелкните **Единый вход**.

    ![Страница интеграции с приложением Slack][4]

2. В диалоговом окне **Единый вход** в списке **Режим** выберите значение **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. В разделе **Домены и URL-адреса приложения Slack** выполните следующие действия.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)
  1. В поле **URL-адрес входа** введите URL-адрес, соответствующий формату _https://<company name>. slack.com_.
  2. В поле **Идентификатор** введите значение **https://slack.com**.

     >[!NOTE]
     >Приведенные выше значения используются только для примера. Мы рекомендуем использовать вместо них уникальные значения URL-адреса и идентификатора. Позже вы сможете заменить эти значения фактическими значениями URL-адреса и идентификатора. Чтобы получить эти значения, обратитесь к [службе поддержки Slack](https://slack.com/help/contact).
     >
     >

4. Приложение Slack ожидает, что утверждения Security Assertion Markup Language (SAML) будут отображаться в определенном формате. Настройте утверждения и установите значения атрибутов в разделе **Атрибуты пользователя** на странице интеграции с приложением Slack, как показано на следующем снимке экрана.

    ![Настройка утверждений в разделе атрибутов пользователя](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. В диалоговом окне **Единый входа** в разделе **Атрибуты пользователя** выберите значение **user.mail** для параметра **Идентификатор пользователя**. Для каждой строки в этой таблице выполните следующие действия.

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    1. Нажмите кнопку **Добавить атрибут**.

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    2. В диалоговом окне **Добавление атрибута** введите в поле **Имя** первое имя из столбца таблицы **Имя атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    3. В поле **Значение** введите первое значение из столбца таблицы **Значение атрибута**.
    4. Нажмите кнопку **ОК**.
    5. Повторите шаги от "a" до "d" для следующих трех строк таблицы.

6. В разделе **Сертификат подписи SAML** щелкните **Создать сертификат**.

    ![Создание сертификата](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. В диалоговом окне **Создать сертификат** щелкните кнопку **Календарь**, выберите дату окончания срока действия и нажмите **Сохранить**.

    ![Выберите дату окончания срока действия сертификата](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. В разделе **Сертификат подписи SAML** поставьте флажок **Сделать новый сертификат активным** и нажмите **Сохранить**.

    ![Активация сертификата подписи SAML](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. Во всплывающем окне **Сертификат восстановления** нажмите кнопку **ОК**.

    ![Всплывающее окно "Сертификат восстановления"](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. В разделе **Сертификат подписи SAML** щелкните **Сертификат (base64)** и сохраните файл сертификата на локальном диске.

    ![Сохранение файла сертификата на локальный диск](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. В разделе **Конфигурация Slack** щелкните **Настроить Slack**, чтобы открыть окно **Настройка единого входа**.

    ![Кнопка "Настроить Slack" для входа в окно настройки единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![Окно "Настройка единого входа"](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. Откройте новое окно браузера и войдите на корпоративный сайт Slack в качестве администратора.

13. Выберите **Microsoft Azure AD**, а затем **Параметры команды**.

    ![Кнопка "Параметры команды" Microsoft Azure AD на корпоративном сайте Slack](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. В разделе **Параметры команды** выберите вкладку **Проверка подлинности**, а затем щелкните **Изменить параметры**.

    ![Кнопка "Изменить параметры" на странице параметров команды](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. В диалоговом окне **Параметры проверки подлинности SAML** сделайте следующее:

    ![Диалоговое окно "Параметры проверки подлинности SAML"](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)
  1. В текстовом поле **SAML 2.0 Endpoint (HTTP)** (Конечная точка HTTP для SAML 2.0) введите значение **SAML Single Sign-On Service URL** (URL-адреса службы единого входа SAML) из окна настройки приложения Azure AD.
  2. В текстовом поле **Identity Provider Issuer** (Издатель поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML) из окна настройки приложения Azure AD.
  3. Откройте загруженный сертификат в блокноте, скопируйте его содержимое и вставьте его в текстовое поле **Общий сертификат**.
  4. Настройте три указанных выше параметра в соответствии с параметрами вашей команды Slack. Дополнительные сведения об этих параметрах см. в разделе [Guide to single sign-on with Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack) (Руководство по использованию единого входа в Slack).
  5. Щелкните **Сохранить конфигурацию**.
  6. Снимите флажок **Разрешить пользователям изменять свой электронный адрес**.
  7. Установите флажок **Разрешить пользователям выбирать свое имя**.
  8. Для параметра **Authentication for your team must be used by** (Проверка подлинности для команды должна использоваться) выберите значение **It’s optional** (Необязательно).
  
### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе вы создадите на портале Azure тестового пользователя с именем Britta Simon, выполнив следующие действия.

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка Azure Active Directory](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.

    ![Кнопка "Все пользователи" в Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. В верхней части диалогового окна **Все пользователи** нажмите кнопку **Добавить**.

    ![Кнопка "Добавить" в диалоговом окне добавления пользователей](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** введите такие сведения:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)
  1. В поле **Имя** введите **BrittaSimon**.
  2. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.
  3. Выберите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
  4. Щелкните **Создать**.

### <a name="create-a-slack-test-user"></a>Создание тестового пользователя Slack

В этом разделе вы создадите в Slack пользователя с именем Britta Simon. Приложение Slack поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Slack, он создается при попытке доступа к приложению Slack.

>[!NOTE]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки Slack](https://slack.com/help/contact).
>
>

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе вы разрешите пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Slack.

![Назначение пользователя для единого входа Azure][200]

Чтобы назначить пользователя Britta Simon в Slack, выполните следующие действия.

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, затем в раздел **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке **Приложения** выберите **Slack**.

    ![Список приложений на портале Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. В области слева выберите **Пользователи и группы**.

    ![Кнопка "Пользователи и группы" в области слева][202]

4. Нажмите кнопку **Добавить**, затем в колонке **Добавление назначения** щелкните **Пользователи и группы**.

    ![Кнопка "Добавить" и колонка "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. Нажмите кнопку **Выбрать**.

7. В колонке **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе вы с помощью панели доступа выполните проверку конфигурации единого входа Azure AD.

Чтобы проверить конфигурацию, перейдите к панели доступа и нажмите плитку **Slack**. Пользователь автоматически войдет в приложение Slack.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


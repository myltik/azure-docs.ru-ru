---
title: "Руководство по интеграции Azure Active Directory с E Sales Manager Remix | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и E Sales Manager Remix."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Руководство по интеграции Azure Active Directory с E Sales Manager Remix

В этом руководстве описано, как интегрировать E Sales Manager Remix с Azure Active Directory (Azure AD).

Интеграция Azure AD с E Sales Manager Remix обеспечивает следующие преимущества.

- С помощью Azure AD можно контролировать доступ к E Sales Manager Remix.
- Вы можете включить автоматический вход пользователей в E Sales Manager Remix (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с E Sales Manager Remix, вам потребуется следующее.

- подписка Azure AD;
- подписка E Sales Manager Remix с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление E Sales Manager Remix из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Добавление E Sales Manager Remix из коллекции
Чтобы настроить интеграцию E Sales Manager Remix с Azure AD, необходимо добавить E Sales Manager Remix из коллекции в список управляемых приложений SaaS.

**Чтобы добавить E Sales Manager Remix из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **E Sales Manager Remix** и выберите **E Sales Manager Remix** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![E Sales Manager Remix в списке результатов](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в E Sales Manager Remix с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в E Sales Manager Remix соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в E Sales Manager Remix.

Чтобы настроить и проверить единый вход Azure AD в E Sales Manager Remix, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя E Sales Manager Remix](#create-an-e-sales-manager-remix-test-user)** требуется для того, чтобы в E Sales Manager Remix существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении E Sales Manager Remix.

**Чтобы настроить единый вход Azure AD в E Sales Manager Remix, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **E Sales Manager Remix** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. В разделе **Домены и URL-адреса приложения E Sales Manager Remix** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<Server-Based-URL>/<sub-domain>/`

    c. Скопируйте значение **Идентификатор** в Блокнот. Данное значение будет использоваться далее в этом руководстве.
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Выберите **Просмотреть и изменить все другие атрибуты пользователей** и щелкните атрибут **emailaddress**.
    
    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Скопируйте значения **Namespace** и **Name** утверждения из текстового поля. Создайте значение по следующей схеме: `<Namespace>/<Name>`. Данное значение понадобится позже в этом руководстве.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. В разделе **Конфигурация E Sales Manager Remix** щелкните **Настройка E Sales Manager Remix**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Войдите в приложение E Sales Manager Remix в качестве администратора.

10. Выберите **To Administrator Menu** (К меню администратора) в меню в правом верхнем углу.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Выберите **System settings** (Параметры системы) > **Cooperation with external system** (Взаимодействие с внешней системой).

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Выберите **SAML**.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. В разделе **SAML authentication setting** (Параметры аутентификации SAML) сделайте следующее.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Выберите **PC version** (Версия ПК).
    
    Б. Выберите **email** (Электронная почта) из раскрывающегося списка в разделе "Collaboration item" (Элемент совместной работы).

    c. В текстовое поле "Collaboration item" (Элемент совместной работы) вставьте **значение утверждения**, скопированное на Azure портале. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. В текстовое поле **Issuer (entity ID)** (Издатель (идентификатор сущности)) вставьте значение **Идентификатор**, скопированное на портале Azure в разделе **Домены и URL-адреса приложения E Sales Manager Remix**.

    д. Чтобы передать скачанный с портала Azure **сертификат**, щелкните **File selection** (Выбор файла).

    f. В текстовое поле **ID provider login URL** (URL-адрес входа поставщика удостоверений) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    ж. В текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    h. Щелкните **Setting complete** (Завершить настройку).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Создание тестового пользователя E Sales Manager Remix

1. Войдите в приложение E Sales Manager Remix в качестве администратора.

2. Выберите **To Administrator Menu** (К меню администратора) в меню в правом верхнем углу.

    ![Конфигурация E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Выберите **Your company settings** (Параметры вашей компании) > **Maintenance of departments and employees** (Обслуживание отделов и сотрудников) и выберите **Employees registered** (Зарегистрированные сотрудники).

    ![Пользователь](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. В разделе **New employee registration** (Регистрация нового пользователя) выполните следующее.
    
    ![Пользователь](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. В текстовое поле **Employee Name** (Имя сотрудника) введите имя пользователя, например Britta.

    Б. Заполните все соответствующие обязательные поля данными пользователя.
    
    c. Если вы включите SAML, администратор не сможет входить в систему с экрана входа. Поэтому предоставьте пользователю привилегии входа администратора, выбрав **Admin Login** (Вход с правами администратора).

    d. Щелкните **Registration** (Регистрация).

5. В будущем, если потребуется выполнить вход от имени администратора, можно будет войти как пользователь, которому были предоставлены разрешения администратора, и щелкнуть **To Administrator Menu** (К меню администратора) в меню в правом верхнем углу.

    ![Пользователь](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к E Sales Manager Remix.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в E Sales Manager Remix, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **E Sales Manager Remix**.

    ![Ссылка на E Sales Manager Remix в списке "Приложения"](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "E Sales Manager Remix" на панели доступа, вы автоматически войдете в приложение E Sales Manager Remix.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png


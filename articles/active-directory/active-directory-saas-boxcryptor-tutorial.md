---
title: Руководство по интеграции Azure Active Directory с Boxcryptor | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Boxcryptor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c46aa523-b58c-4a95-a800-db2e5e01c542
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jeedes
ms.openlocfilehash: cf8da8d7b0221f9a6bef007fafa2a127e13097e8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34336965"
---
# <a name="tutorial-azure-active-directory-integration-with-boxcryptor"></a>Руководство. Интеграция Azure Active Directory с Boxcryptor

В этом руководстве описано, как интегрировать Boxcryptor с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Boxcryptor обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Boxcryptor.
- Вы можете включить автоматический вход пользователей в Boxcryptor (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Boxcryptor, вам потребуются:

- подписка Azure AD;
- подписка Boxcryptor с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Boxcryptor из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-boxcryptor-from-the-gallery"></a>Добавление Boxcryptor из коллекции.
Чтобы настроить интеграцию Boxcryptor с Azure AD, необходимо добавить Boxcryptor из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Boxcryptor из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Boxcryptor**, выберите **Boxcryptor** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Boxcryptor в списке результатов](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Boxcryptor с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Boxcryptor соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и его представлением в Boxcryptor.

Чтобы настроить и проверить единый вход Azure AD в Boxcryptor, выполните следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Boxcryptor](#create-a-boxcryptor-test-user)** требуется для того, чтобы в Boxcryptor существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Boxcryptor.

**Чтобы настроить единый вход Azure AD в Boxcryptor, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Boxcryptor** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Boxcryptor** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Boxcryptor](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://www.boxcryptor.com/app`.

    Б. В текстовом поле **Идентификатор** введите значение: `boxcryptor`

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Boxcryptor** щелкните **Настроить Boxcryptor**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка Andromeda SCM](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_configure.png)

7. Чтобы настроить единый вход на стороне **Boxcryptor**, нужно отправить скачанный **сертификат в кодировке Base64**, **URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** в [службу поддержки Boxcryptor](mailto:support@boxcryptor.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-boxcryptor-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-boxcryptor-test-user"></a>Создание тестового пользователя Boxcryptor

В этом разделе описано, как создать пользователя Britta Simon в Boxcryptor. Обратитесь в [службу поддержки Boxcryptor](mailto:support@boxcryptor.com), чтобы добавить домен или пользователей, которых нужно включить в список разрешений на платформе Boxcryptor. Если служба поддержки добавит домен, пользователи будут автоматически подготовлены для платформы Boxcryptor. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Boxcryptor.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Boxcryptor, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Boxcryptor**.

    ![Ссылка на Boxcryptor в списке "Приложения"](./media/active-directory-saas-boxcryptor-tutorial/tutorial_boxcryptor_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Boxcryptor на панели доступа, вы автоматически войдете в приложение Boxcryptor.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boxcryptor-tutorial/tutorial_general_203.png


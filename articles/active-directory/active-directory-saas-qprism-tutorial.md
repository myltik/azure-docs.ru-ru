---
title: Руководство по интеграции Azure Active Directory с QPrism | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: b924ea1df926518ba1d86909f8e6a78deabd5468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140054"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Руководство по интеграции Azure Active Directory с QPrism

В этом руководстве описано, как интегрировать QPrism с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением QPrism обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к QPrism.
- Вы можете включить автоматический вход пользователей в QPrism (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с QPrism, вам потребуется:

- подписка Azure AD;
- подписка QPrism с поддержкой единого входа.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление QPrisme из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="add-qprism-from-the-gallery"></a>Добавление QPrism из коллекции
Чтобы настроить интеграцию QPrism с Azure AD, необходимо добавить QPrism из коллекции в список управляемых приложений SaaS.

**Чтобы добавить QPrism из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **QPrism** и выберите **QPrism** на панели результатов. Нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![QPrism в списке результатов](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в QPrism с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в QPrism соответствует пользователю в Azure AD. Иными словами, должна существовать связь между пользователем Azure AD и соответствующим пользователем в QPrism.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в QPrism.

Чтобы настроить и проверить единый вход Azure AD в QPrism, выполните инструкции в следующих базовых блоках.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя QPrism](#create-a-qprism-test-user) требуется для того, чтобы в QPrism существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) позволяет убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении QPrism.

1. На портале Azure на странице интеграции с приложением **QPrism** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить единый вход, в диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. В разделе **Домены и URL-адреса приложения QPrism** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<customer domain>.qmyzone.com/login`.

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<customer domain>.qmyzone.com/metadata.php`.
         
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим идентификатором и URL-адресом для входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов QPrism](mailto:qsupport-ce@quatrro.com). 

4. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложения**, и вставьте его в Блокнот.

     ![Ссылка для скачивания сертификата](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_certificate.png)

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Чтобы настроить единый вход на стороне **QPrism**, отправьте [группе поддержки QPrism](mailto:qsupport-ce@quatrro.com) **URL-адрес метаданных федерации приложения**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Создание тестового пользователя в Azure AD**

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** нажмите кнопку **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-qprism-test-user"></a>Создание тестового пользователя QPrism

В этом разделе описано, как создать пользователя Britta Simon в приложении QPrism. Обратитесь к [группе поддержки QPrism](mailto:qsupport-ce@quatrro.com), чтобы добавить пользователей на платформу QPrism. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к QPrism.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в QPrism, выполните следующие действия.**

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Перейдите к элементу **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **QPrism**.

    ![Ссылка на QPrism в списке приложений](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Выберите **Добавить**. В области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** в списке **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В области **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкните плитку QPrism на панели доступа, чтобы автоматически войти в приложение QPrism.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png


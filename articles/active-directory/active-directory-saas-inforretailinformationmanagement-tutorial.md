---
title: Руководство. Интеграция Azure Active Directory с приложением Infor Retail – Information Management | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Infor Retail – Information Management.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 7aecd00032e7e16d6f1586e409fa552c4f0795d7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341166"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Руководство. Интеграция Azure Active Directory с приложением Infor Retail – Information Management

В этом руководстве описано, как интегрировать приложение Infor Retail – Information Management с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Infor Retail – Information Management обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Infor Retail – Information Management.
- Вы можете включить автоматический вход пользователей в Infor Retail – Information Management (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Infor Retail – Information Management, вам потребуется следующее:

- подписка Azure AD;
- подписка Infor Retail – Information Management с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Infor Retail – Information Management из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Добавление Infor Retail – Information Management из коллекции
Чтобы настроить интеграцию Infor Retail – Information Management с Azure AD, необходимо добавить Infor Retail – Information Management из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Infor Retail – Information Management из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Infor Retail – Information Management**, на панели результатов выберите **Infor Retail – Information Management** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Infor Retail – Information Management в списке результатов](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure AD в Infor Retail – Information Management с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Infor Retail – Information Management соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Infor Retail – Information Management.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Infor Retail – Information Management.

Чтобы настроить и проверить единый вход Azure AD в Infor Retail – Information Management, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Infor Retail – Information Management](#create-an-infor-retail--information-management-test-user)** требуется для создания пользователя Britta Simon в Infor Retail – Information Management, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Infor Retail – Information Management.

**Чтобы настроить единый вход Azure AD в Infor Retail – Information Management, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Infor Retail – Information Management** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, то в разделе **Домены и URL-адреса приложения Infor Retail – Information Management** сделайте следующее:

    ![Поставщик удостоверений со сведениями для единого входа в разделе "Домены и URL-адреса приложения Infor Retail – Information Management"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    |   |
    | -- |
    | `https://<company name>.mingle.infor.com` |
    | `http://<company name>.mingledev.infor.com` |

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.mingle.infor.com/sp/ACS.saml2`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Поставщика услуг со сведениями для единого входа в разделе "Домены и URL-адреса приложения Infor Retail – Information Management"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.mingle.infor.com/<company code>`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Infor Retail – Information Management](mailto:innovate@infor.com). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_400.png)
    
7. Чтобы настроить единый вход на стороне **Infor Retail – Information Management** , необходимо отправить скачанный **XML-файл метаданных** в [службу поддержки Infor Retail – Information Management](mailto:innovate@infor.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-infor-retail--information-management-test-user"></a>Создание тестового пользователя Infor Retail – Information Management

В этом разделе описано, как создать пользователя Britta Simon в приложении Infor Retail – Information Management. Обратитесь в [службу поддержки Infor Retail – Information Management](mailto:innovate@infor.com), чтобы добавить пользователей на платформу Infor Retail – Information Management.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Infor Retail – Information Management.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Infor Retail – Information Management, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Infor Retail – Information Management**.

    ![В списке приложений выберите Infor Retail – Information Management.](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Infor Retail – Information Management на панели доступа, вы автоматически войдете в приложение Infor Retail – Information Management.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_203.png


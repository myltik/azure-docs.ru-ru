---
title: Учебник. Интеграция Azure Active Directory с eDigitalResearch | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении eDigitalResearch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c6b66ea0-16ba-45b4-b550-e81c56262b1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 748b630dbad217ec3525db70b1dca22302b1c516
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342024"
---
# <a name="tutorial-azure-active-directory-integration-with-edigitalresearch"></a>Руководство. Интеграция Azure Active Directory с eDigitalResearch

В этом руководстве описано, как интегрировать eDigitalResearch с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением eDigitalResearch дает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к eDigitalResearch.
- Можно включить автоматический вход пользователей в eDigitalResearch (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с eDigitalResearch, вам потребуется:

- подписка Azure AD;
- подписка на eDigitalResearch с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление eDigitalResearch из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-edigitalresearch-from-the-gallery"></a>Добавление eDigitalResearch из коллекции.
Чтобы настроить интеграцию eDigitalResearch с Azure AD, необходимо добавить приложение eDigitalResearch из коллекции в список управляемых приложений SaaS.

**Чтобы добавить eDigitalResearch из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **eDigitalResearch**, выберите **eDigitalResearch** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![eDigitalResearch в списке результатов](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в eDigitalResearch с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в eDigitalResearch соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в eDigitalResearch.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в eDigitalResearch.

Чтобы настроить и проверить единый вход Azure AD в eDigitalResearch, выполните следующие стандартные действия:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя eDigitalResearch](#create-a-edigitalresearch-test-user)** требуется для того, чтобы в eDigitalResearch существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверьте единый вход](#test-single-sign-on)**, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении eDigitalResearch.

**Чтобы настроить единый вход Azure AD в eDigitalResearch, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **eDigitalResearch** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_samlbase.png)

3. В разделе **Домены и URL-адреса приложения eDigitalResearch** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения eDigitalResearch](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company-name>.edigitalresearch.com`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company-name>.edigitalresearch.com/login/consume`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки eDigitalResearch](http://www.maruedr.com/contact).
 


4. В разделе **Сертификат для подписи токена SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    !![Ссылка для скачивания сертификата](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_400.png)

6. В разделе **Настройка eDigitalResearch** щелкните **Настроить eDigitalResearch**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и идентификатор сущности SAML** из раздела **Краткий справочник**.

    ![Настройка eDigitalResearch](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_configure.png) 

7. Чтобы настроить единый вход на стороне **eDigitalResearch**, нужно отправить скачанный **файл сертификата (Base64)**, **идентификатор сущности SAML** и **URL-адрес выхода** в [службу поддержки eDigitalResearch](http://www.maruedr.com/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-edigitalresearch-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-edigitalresearch-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-edigitalresearch-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-edigitalresearch-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-edigitalresearch-test-user"></a>Создание тестового пользователя eDigitalResearch

Цель этого раздела — создать пользователя с именем Britta Simon в приложении eDigitalResearch. 

Чтобы создать пользователей, обратитесь в [службу поддержки eDigitalResearch](http://www.maruedr.com/contact).     
    
 > [!NOTE]
 > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к eDigitalResearch.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в eDigitalResearch, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **eDigitalResearch**.

    ![Ссылка eDigitalResearch в списке приложений](./media/active-directory-saas-edigitalresearch-tutorial/tutorial_edigitalresearch_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент eDigitalResearch на панели доступа, вы автоматически войдете в приложение eDigitalResearch.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-edigitalresearch-tutorial/tutorial_general_203.png


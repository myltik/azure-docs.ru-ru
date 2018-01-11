---
title: "Учебник: Интеграция Azure Active Directory с IBM OpenPages | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и IBM OpenPages."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 74d5dea5-2c48-464a-a7d0-cdd481c429d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 6b96a54bad2166a15574cf015f1ee586cf6fcc35
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-openpages"></a>Учебник: Интеграция Azure Active Directory с IBM OpenPages

В этом учебнике вы узнаете, как интегрировать IBM OpenPages с Azure Active Directory (Azure AD).

Интеграция IBM OpenPages с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к IBM OpenPages.
- Вы можете включить их учетные записи Azure AD пользователям автоматически получить вошедшего в IBM OpenPages (Single Sign-On).
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Настройка интеграции Azure AD с IBM OpenPages, необходимы следующие элементы:

- подписка Azure AD;
- IBM OpenPages единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление IBM OpenPages из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ibm-openpages-from-the-gallery"></a>Добавление IBM OpenPages из галереи
Для настройки интеграции IBM OpenPages в Azure AD, необходимо добавить IBM OpenPages из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IBM OpenPages из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **IBM OpenPages**выберите **IBM OpenPages** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![IBM OpenPages в списке результатов](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_ibmopenpage_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе можно настроить и тест Azure AD единого входа с IBM OpenPages на основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в IBM OpenPages для пользователя в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в IBM OpenPages необходимо установить.

В IBM OpenPages, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с IBM OpenPages, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя, прошедшего IBM OpenPages](#create-an-ibm-openpages-test-user)**  — на аналог Саймон Britta в IBM OpenPages, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении IBM OpenPages.

**Чтобы настроить Azure AD единого входа с IBM OpenPages, выполните следующие действия.**

1. На портале Azure на **IBM OpenPages** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_ibmopenpage_samlbase.png)

3. На **URL-адреса и домена OpenPages IBM** выполните следующие действия:

    ![URL-адреса и IBM OpenPages домена единого входа сведения](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_ibmopenpage_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `http://<subdomain>.ibm.com:<ID>/openpages`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.ibm.com:<ID>/samlsps/op`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Обратитесь к [группа поддержки клиента OpenPages IBM](https://www.ibm.com/support/home/) для получения этих значений. 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_ibmopenpage_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_400.png)

7. Для настройки единого входа на **IBM OpenPages** стороны, необходимо отправить загруженного **метаданные в формате XML** для [IBM OpenPages поддержки](https://www.ibm.com/support/home/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-ibmopenpages-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-ibmopenpages-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-ibmopenpages-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-ibmopenpages-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-ibm-openpages-test-user"></a>Создание тестового пользователя, прошедшего IBM OpenPages

В этом разделе создайте пользователя с именем Саймон Britta в IBM OpenPages. Работать с [IBM OpenPages поддержки](https://www.ibm.com/support/home/) для добавления пользователей в платформе IBM OpenPages. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к IBM OpenPages.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta IBM OpenPages, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **IBM OpenPages**.

    ![IBM OpenPages ссылку в список приложений](./media/active-directory-saas-ibmopenpages-tutorial/tutorial_ibmopenpage_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки IBM OpenPages плитки на панели доступа, вы должны получить автоматически вошедшего в приложение IBM OpenPages.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ibmopenpages-tutorial/tutorial_general_203.png


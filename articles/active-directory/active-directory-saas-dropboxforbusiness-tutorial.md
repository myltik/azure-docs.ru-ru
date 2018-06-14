---
title: Руководство по интеграции Azure Active Directory с Dropbox for Business | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: aa00a88f8325345b1b45d7d0971a03590bce1029
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342636"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Руководство. Интеграция Azure Active Directory с Dropbox for Business

В этом руководстве описано, как интегрировать Dropbox for Business с Azure Active Directory (Azure AD).

Интеграция Dropbox for Business с Azure AD обеспечивает перечисленные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению Dropbox for Business.
- Вы можете включить автоматический вход пользователей в Dropbox for Business (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Dropbox for Business, вам потребуется:

- подписка Azure AD;
- подписка Dropbox for Business с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Dropbox for Business из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Добавление Dropbox for Business из коллекции
Чтобы настроить интеграцию Dropbox for Business с Azure AD, необходимо добавить Dropbox for Business из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Dropbox for Business из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Dropbox for Business**, выберите **Dropbox for Business** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Dropbox for Business в списке результатов](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Dropbox for Business с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Dropbox for Business соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Dropbox for Business.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Dropbox for Business.

Чтобы настроить и проверить единый вход Azure AD в Dropbox for Business, вам потребуется выполнить действия в перечисленных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Dropbox for Business](#create-a-dropbox-for-business-test-user)** требуется для создания пользователя Britta Simon в Dropbox for Business, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Dropbox for Business.

**Чтобы настроить единый вход Azure AD в Dropbox for Business, выполните указанные ниже действия.**

1. На портале Azure на странице интеграции с приложением **Dropbox for Business** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Dropbox for Business** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.dropbox.com/sso/<id>`

    Б. В текстовом поле **Идентификатор** введите значение: `Dropbox`

    > [!NOTE] 
    > Приведенное выше значение "URL-адрес для входа" используется только для примера. Вы замените это значение на фактический URL-адрес для входа, который описывается далее в этом руководстве. Для получения этого значения обратитесь в [службу поддержки клиентов Dropbox for Business](https://www.dropbox.com/business/contact). 
 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Dropbox for Business** щелкните **Настроить Dropbox for Business**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Чтобы настроить единый вход на стороне **Dropbox for Business**, перейдите к клиенту Dropbox for Business.

    a. Войдите в клиент Dropbox for Business. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "Настройка единого входа")
   
    Б. На панели навигации слева щелкните **Консоль администрирования**. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "Настройка единого входа")
   
    c. В **консоли администрирования** выберите пункт **Проверка подлинности** в левой области навигации. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "Настройка единого входа")
   
    d. В разделе **Единый вход** установите флажок **Включить единый вход** и нажмите кнопку **Дополнительно**, чтобы развернуть этот раздел.  
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "Настройка единого входа")
   
    д. Скопируйте URL-адрес рядом со строкой **Users can sign in by entering their email address or they can go directly to** (Пользователи могут выполнить вход, введя свой адрес электронной почты, или непосредственно перейти к) и вставьте его в текстовое поле **URL-адрес входа** в разделе **Домены и URL-адреса приложения Dropbox for Business** на портале Azure. 
    
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. В разделе **Single sign-on** (Единый вход) на странице **Authentication** (Проверка подлинности) выполните следующие действия. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Настройка единого входа")
   
    a. Установите флажок **Обязательно**.
   
    Б. В текстовое поле **Sign-in URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. Щелкните **Choose certificate** (Выбрать сертификат), а затем выберите **файл сертификата в кодировке Base64**.

    d. Щелкните **Сохранить изменения**, чтобы завершить настройку клиента Dropbox for Business.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Создание тестового пользователя Dropbox for Business

В этом разделе вы создадите в Dropbox for Business пользователя с именем Britta Simon. Приложение Dropbox for Business поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Dropbox for Business еще не существует, он создается при попытке доступа к приложению Dropbox for Business.

>[!Note]
>Если вам нужно вручную создать пользователя, обратитесь в [службу поддержки клиентов Dropbox for Business](https://www.dropbox.com/business/contact). 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Dropbox for Business, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Dropbox for Business, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Dropbox for Business**.

    ![Ссылка на Dropbox for Business в списке приложений](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При выборе плитки Dropbox for Business на панели доступа должна появиться страница входа приложения Dropbox for Business.
 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png


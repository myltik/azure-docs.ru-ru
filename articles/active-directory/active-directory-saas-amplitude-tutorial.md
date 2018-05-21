---
title: Руководство по интеграции Azure Active Directory с Amplitude | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeedes
ms.openlocfilehash: 711660b2360503c2dfa7b11e275cf5928983b688
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>Руководство по интеграции Azure Active Directory с Amplitude

В этом руководстве описано, как интегрировать Amplitude с Azure Active Directory (Azure AD).

Интеграция Amplitude с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Amplitude.
- Вы можете включить автоматический вход пользователей в Amplitude (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Amplitude, вам потребуется:

- подписка Azure AD;
- подписка Amplitude с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Amplitude из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-amplitude-from-the-gallery"></a>Добавление Amplitude из коллекции.
Чтобы настроить интеграцию Amplitude с Azure AD, необходимо добавить Amplitude из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amplitude из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Amplitude**, выберите **Amplitude** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Amplitude в списке результатов](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amplitude с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Amplitude соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Amplitude.

Чтобы настроить и проверить единый вход Azure AD в Amplitude, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Amplitude](#create-an-amplitude-test-user)** нужно для того, чтобы в Amplitude существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amplitude.

**Чтобы настроить единый вход Azure AD в Amplitude, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Amplitude** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Amplitude** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Amplitude](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://amplitude.com/saml/sso/metadata`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://analytics.amplitude.com/saml/sso/<uniqueid>`.

    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Далее в этом руководстве объясняется, как получить значение URL-адреса ответа.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Amplitude](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://analytics.amplitude.com/sso`

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-amplitude-tutorial/tutorial_general_400.png)
    
7. Войдите на корпоративный сайт Amplitude с правами администратора.

8. Щелкните **Plan Admin** (Администратор плана) на панели навигации слева.

    ![Настройка единого входа](./media/active-directory-saas-amplitude-tutorial/configure1.png)

9. Выберите **Microsoft Azure Active Directory Metadata** (Метаданные Microsoft Azure Active Directory) в разделе **SSO Integration** (Интеграция единого входа).

    ![Настройка единого входа](./media/active-directory-saas-amplitude-tutorial/configure2.png)

10. В разделе **Set Up Single Sign-On** (Настройка единого входа) сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-amplitude-tutorial/configure3.png)

    a. Откройте в Блокноте **XML-файл метаданных**, скачанный с портала Azure, и вставьте его содержимое в текстовое поле **Microsoft Azure Active Directory Metadata** (Метаданные Microsoft Azure Active Directory).

    Б. Скопируйте значение **Reply URL (ACS)** (URL-адрес ответа (ACS)) и вставьте его в текстовое поле **URL-адрес ответа** для домена Amplitude на портале Azure.

    c. Нажмите кнопку **Сохранить**

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-amplitude-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-amplitude-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-amplitude-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-amplitude-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-amplitude-test-user"></a>Создание тестового пользователя в Amplitude

Цель этого раздела — создать пользователя с именем Britta Simon в Amplitude. Приложение Amplitude поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Amplitude (если он еще не создан).
>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки Amplitude](https://amplitude.zendesk.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Amplitude.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Amplitude, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Amplitude**.

    ![Ссылка на Amplitude в списке "Приложения"](./media/active-directory-saas-amplitude-tutorial/tutorial_amplitude_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amplitude на панели доступа, вы автоматически войдете в приложение Amplitude.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amplitude-tutorial/tutorial_general_203.png


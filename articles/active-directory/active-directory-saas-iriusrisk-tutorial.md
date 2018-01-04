---
title: "Учебник: Интеграция Azure Active Directory с IriusRisk | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и IriusRisk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2c854d5-101d-4d67-80e0-87749e1a0352
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: jeedes
ms.openlocfilehash: e33d584b3e7eb939d669253e8c6ef40fd4f963c9
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iriusrisk"></a>Учебник: Интеграция Azure Active Directory с IriusRisk

В этом учебнике вы узнаете, как интегрировать IriusRisk с Azure Active Directory (Azure AD).

Интеграция с Azure AD IriusRisk предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к IriusRisk.
- Позволяет пользователям автоматически получить вошедшего в IriusRisk (Single Sign-On) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с IriusRisk, необходимы следующие элементы:

- подписка Azure AD;
- IriusRisk единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление IriusRisk из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-iriusrisk-from-the-gallery"></a>Добавление IriusRisk из галереи
Чтобы настроить интеграцию IriusRisk в Azure AD, необходимо добавить IriusRisk из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IriusRisk из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **IriusRisk**выберите **IriusRisk** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![IriusRisk в списке результатов](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с IriusRisk основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в IriusRisk для пользователя в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в IriusRisk необходимо установить.

В IriusRisk, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с IriusRisk, необходимые для выполнения следующих блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя IriusRisk](#create-a-iriusrisk-test-user)**  — на аналог Саймон Britta в IriusRisk, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении IriusRisk.

**Чтобы настроить Azure AD единого входа с IriusRisk, выполните следующие действия.**

1. На портале Azure на **IriusRisk** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_samlbase.png)

3. На **URL-адреса и домена IriusRisk** выполните следующие действия:

    ![URL-адреса и IriusRisk домена единого входа сведения](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.iriusrisk.com/ui#!login`

    Б. В текстовом поле **Идентификатор** введите значение `iriusrisk-sp`.

    > [!NOTE] 
    > Значение URL-адрес входа не реальными. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [IriusRisk клиента поддержки](mailto:info@continuumsecurity.net) для получения этого значения. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_400.png)

6. Для настройки единого входа на **IriusRisk** стороны, необходимо отправить загруженного **метаданные в формате XML** для [IriusRisk поддержки](mailto:info@continuumsecurity.net). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-iriusrisk-test-user"></a>Создание тестового пользователя IriusRisk

Цель этого раздела является создание пользователя с именем Саймон Britta в IriusRisk. IriusRisk поддерживает подготовку just-in-time, который по умолчанию включен. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к IriusRisk, если он еще не существует, создается новый пользователь.

> [!Note]
> Если необходимо вручную создать пользователя, обратитесь в службу [IriusRisk поддержки](mailto:info@continuumsecurity.net).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к IriusRisk.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta IriusRisk, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **IriusRisk**.

    ![IriusRisk ссылку в список приложений](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки IriusRisk плитки на панели доступа, вы должны получить автоматически вошедшего в приложение IriusRisk.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_203.png


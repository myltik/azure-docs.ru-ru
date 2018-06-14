---
title: Учебник. Интеграция Azure Active Directory с Front | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Front.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: cc4499043ed7fbff964549fe56ecee641f7eadf3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340467"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Учебник. Интеграция Azure Active Directory с Front

В этом руководстве описано, как интегрировать Front с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Front обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Front.
- Вы можете включить автоматический вход пользователей в Front (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Front, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Front.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Front из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-front-from-the-gallery"></a>Добавление Front из коллекции
Чтобы настроить интеграцию Front с Azure AD, необходимо добавить Front из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Front из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Front**, выберите **Front** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Front в списке результатов](./media/active-directory-saas-front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Front с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Front соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Front.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Front.

Чтобы настроить и проверить единый вход Azure AD в Front, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Front](#create-a-front-test-user)** требуется для того, чтобы во Front существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Front.

**Чтобы настроить единый вход Azure AD в Front, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Front** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-front-tutorial/tutorial_front_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Front** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.frontapp.com`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.frontapp.com/sso/saml/callback`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Вместо этих значений укажите фактические идентификатор и URL-адрес ответа, к которым мы вернемся позже в этом руководстве, или обратитесь в [службу поддержки клиентов Front](mailto:support@frontapp.com), чтобы получить эти значения. 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Front** щелкните **Настроить Front**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_configure.png) 

7. Войдите в клиент Front с правами администратора.

8. Последовательно выберите пункты **Settings (Параметры) (значок шестеренки в нижней части левой боковой панели) > Preferences (Предпочтения)**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

9. Щелкните ссылку **Single Sign On** (Единый вход).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

10. В раскрывающемся списке **Single Sign On** (Единый вход) выберите **SAML**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

11. В текстовое поле **Entry Point** (Точка входа) введите значение **URL-адрес службы единого входа** из мастера настройки приложения Azure AD.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

12. Откройте скачанный файл **сертификата в кодировке Base64** в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Signing certificate** (Сертификат для подписи).
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

13. В разделе **Service provider settings** (Параметры поставщика услуг) сделайте следующее:

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

    a. Скопируйте значение **Entity ID** (Идентификатор сущности) и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения Front** на портале Azure.

    Б. Скопируйте значение **ACS URL** (URL-адрес ACS) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Front** на портале Azure.
    
14. Нажмите кнопку **Сохранить** .

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-front-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-front-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-front-test-user"></a>Создание тестового пользователя Front

В этом разделе описано, как создать пользователя Britta Simon в приложении Front. Обратитесь в [службу поддержки клиентов Front](mailto:support@frontapp.com), чтобы добавить пользователей на платформу Front. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Front.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Front, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Front**.

    ![Ссылка на Front в списке "Приложения"](./media/active-directory-saas-front-tutorial/tutorial_front_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Front на панели доступа, вы автоматически войдете в приложение Front. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-front-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png


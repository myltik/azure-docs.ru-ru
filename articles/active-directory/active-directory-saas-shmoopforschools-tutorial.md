---
title: Руководство по интеграции Azure Active Directory с Shmoop For Schools | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 799a88344d6c348061af19bfbbd9022025d2d66b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350616"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Руководство по интеграции Azure Active Directory с Shmoop For Schools

В этом руководстве описано, как интегрировать Shmoop For Schools с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Shmoop For Schools обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Shmoop For Schools.
- Вы можете включить автоматический вход пользователей в Shmoop For Schools с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Shmoop For Schools, вам потребуется:

- подписка Azure AD;
- подписка Shmoop For Schools с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

Чтобы протестировать инструкции из этого руководства, рекомендуем:

- использовать рабочую среду только при необходимости;
- если у вас нет пробной среды Azure AD, получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление Shmoop For Schools из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Добавление Shmoop For Schools из коллекции
Чтобы настроить интеграцию Shmoop For Schools с Azure AD, необходимо добавить Shmoop For Schools из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Shmoop For Schools из коллекции, выполните описанные ниже действия.**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Shmoop For Schools**. В результатах выберите **Shmoop For Schools** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Shmoop For Schools в списке результатов](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Shmoop For Schools с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Shmoop For Schools соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Shmoop For Schools.

Чтобы настроить и проверить единый вход Azure AD в Shmoop For Schools, выполните приведенные ниже инструкции.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя приложения Shmoop For Schools](#create-a-shmoop-for-schools-test-user) — требуется, чтобы в Shmoop For Schools существовала учетная запись пользователя (Britta Simon), связанная с представлением пользователя в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Shmoop For Schools.

**Чтобы настроить единый вход Azure AD в Shmoop For Schools, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Shmoop For Schools** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в раскрывающемся списке **Режим единого входа** выберите пункт **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. В разделе **Shmoop For Schools Domain and URLs** (домен и URL-адреса приложения Shmoop For Schools) выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`.

    Б. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://schools.shmoop.com/<uniqueid>`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Shmoop For Schools](mailto:support@shmoop.com). 
 
4. Приложение Shmoop For Schools ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Значениями этих атрибутов можно управлять в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана показано, как настроить утверждения:

    ![Настройка единого входа](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools поддерживает две роли для пользователей — **Учитель** и **Ученик**. Настройте эти роли в Azure AD, чтобы пользователям можно было назначить соответствующие роли. Чтобы понять, как настроить роли в Azure AD, см. статью об [управления доступом на основе ролей в облачных приложениях с помощью Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на предыдущем рисунке.  Затем выполните следующие действия:

    | Имя атрибута | Значение атрибута |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.
    
    ![Настройка единого входа ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. Из списка **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

6. Нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Чтобы настроить единый вход на стороне **Shmoop For Schools**, отправьте [группе поддержки Shmoop For Schools](mailto:support@shmoop.com) **URL-адрес метаданных федерации приложения**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе на портале Azure создается тестовый пользователь с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Создание тестового пользователя Shmoop For Schools

Цель этого раздела — создать в приложении Shmoop For Schools пользователя с именем Britta Simon. Приложение Shmoop For Schools поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если новой учетной записи пользователя нет, она создается при попытке получить доступ к Shmoop For Schools.

>[!NOTE]
>Чтобы создать учетную запись пользователя вручную, обратитесь в [службу поддержки Shmoop For Schools](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Shmoop For Schools, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить учетную запись пользователя Britta Simon приложению Shmoop For Schools, выполните описанные ниже действия.**

1. На портале Azure откройте представление "Приложения". Затем выберите пункт **Корпоративные приложения** в представлении каталога.  Затем выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Shmoop For Schools**.

    ![Ссылка на Shmoop For Schools в списке приложений](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**. 

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку **Shmoop For Schools** на панели доступа, вы автоматически войдете в приложение Shmoop For Schools.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md).
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png


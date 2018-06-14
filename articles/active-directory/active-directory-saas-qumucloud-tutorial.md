---
title: Руководство. Интеграция Azure Active Directory с Qumu Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Qumu Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8c4a97b-4de6-49d4-b64e-42222c2ec6c9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.openlocfilehash: 1778917a383aef2fc0f791cda3235cff74b77f18
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350701"
---
# <a name="tutorial-azure-active-directory-integration-with-qumu-cloud"></a>Руководство. Интеграция Azure Active Directory с Qumu Cloud

В этом руководстве описано, как интегрировать приложение Qumu Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Qumu Cloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Qumu Cloud.
- Вы можете включить автоматический вход пользователей в Qumu Cloud (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Qumu Cloud, вам потребуется:

- подписка Azure AD;
- подписка Qumu Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Qumu Cloud из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-qumu-cloud-from-the-gallery"></a>Добавление Qumu Cloud из коллекции
Чтобы настроить интеграцию Qumu Cloud с Azure AD, необходимо добавить Qumu Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Qumu Cloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Qumu Cloud**, выберите **Qumu Cloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Qumu Cloud в списке результатов](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Qumu Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Qumu Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Qumu Cloud.

Чтобы настроить и проверить единый вход Azure AD в Qumu Cloud, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Qumu Cloud](#create-a-qumu-cloud-test-user)** требуется для того, чтобы в Qumu Cloud существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Qumu Cloud.

**Чтобы настроить единый вход Azure AD в Qumu Cloud, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Qumu Cloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Qumu Cloud** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.qumucloud.com/saml/SSO`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.qumucloud.com/saml/SSO`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.qumucloud.com`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Qumu Cloud](mailto:support@qumu.com).

4. Приложение Qumu Cloud ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-qumucloud-tutorial/attribute.png)
    
5. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей**, чтобы развернуть атрибуты. Выполните следующие действия для каждого отображаемого атрибута.

    | Имя атрибута | Значение атрибута |
    | ---------------| --------------- |    
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |

    a. Щелкните атрибут, чтобы открыть окно **Изменить атрибут**.

    ![Настройка единого входа](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_04.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    ![Настройка единого входа](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_05.png)

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Текстовое поле **Пространство имен** оставьте пустым.
    
    д. Нажмите кнопку **ОК**.

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-qumucloud-tutorial/tutorial_general_400.png)
    
7. Чтобы настроить единый вход на стороне **Qumu Cloud**, отправьте скачанный **XML-файл метаданных** [группе поддержки Qumu Cloud](mailto:support@qumu.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-qumu-cloud-test-user"></a>Создание тестового пользователя Qumu Cloud

Цель этого раздела — создать пользователя с именем Britta Simon в Qumu Cloud. Приложение Qumu Cloud поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к приложению Qumu Cloud (если он еще не существует).
>[!Note]
>Чтобы создать учетную запись пользователя вручную, обратитесь в [службу поддержки Qumu Cloud](mailto:support@qumu.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Qumu Cloud.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Qumu Cloud, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Qumu Cloud**.

    ![Ссылка на Qumu Cloud в списке "Приложения"](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Qumu Cloud на панели доступа, вы автоматически войдете в приложение Qumu Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_203.png


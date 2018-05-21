---
title: Руководство по интеграции Azure Active Directory с ServiceChannel | Документы Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 2b1c7461cb1ed232290cf78a03449611f9ef95da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Руководство: интеграция Azure Active Directory с ServiceChannel

В этом руководстве описано, как интегрировать ServiceChannel с Azure Active Directory (Azure AD).

Интеграция ServiceChannel с Azure AD дает приведенные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ пользователей к ServiceChannel.
- Вы можете включить автоматический вход пользователей в ServiceChannel (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ServiceChannel, вам потребуется:

- подписка Azure AD;
- подписка ServiceChannel с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ServiceChannel из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-servicechannel-from-the-gallery"></a>Добавление ServiceChannel из коллекции
Чтобы настроить интеграцию ServiceChannel с Azure AD, необходимо добавить ServiceChannel из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ServiceChannel из коллекции, сделайте следующее.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **ServiceChannel**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_000.png)

5. На панели результатов выберите **ServiceChannel** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в ServiceChannel с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в ServiceChannel соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ServiceChannel.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ServiceChannel.

Чтобы настроить и проверить единый вход Azure AD в ServiceChannel, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ServiceChannel](#creating-a-servicechannel-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении ServiceChannel.

**Чтобы настроить единый вход Azure AD в ServiceChannel, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **ServiceChannel** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_01.png)

3. В разделе **Домены и URL-адреса приложения ServiceChannel** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. В текстовом поле **Идентификатор** введите значение `http://adfs.<domain>.com/adfs/service/trust`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<customer domain>.servicechannel.com/saml/acs`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки ServiceChannel](https://servicechannel.zendesk.com/hc/en-us).

4. Приложение ServiceChannel ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. **NameIdentifier(идентификатор пользователя)** — это единственное обязательное утверждение, а значение по умолчанию — **user.userprincipalname**, однако ServiceChannel ожидает его сопоставления с **user.mail**. Если вы планируете включить JIT-подготовку пользователей, следует добавить следующие утверждения, как показано ниже. Утверждение **Роль** должно быть сопоставлено с **user.assignedroles**, содержащим роль пользователя.  

    Дополнительные инструкции по работе с утверждениями можно найти в руководстве по ServiceChannel [здесь](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example).
    
    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Перейдите по [этой ссылке](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/), чтобы прочитать о настройке **роли** в Azure AD.

5. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и задайте эти атрибуты.

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | Роль| user.assignedroles |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.
    
6. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. Выберите команду **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial_general_400.png)

8. В разделе **Настройка ServiceChannel** щелкните **Настроить ServiceChannel**, чтобы открыть окно **Настройка единого входа**. Запишите **идентификатор сущности SAML** из раздела **Краткий справочник**.

9. Для настройки единого входа на стороне **ServiceChannel** необходимо отправить загруженный **сертификат (Base64)** и **идентификатор сущности SAML** в [службу поддержки ServiceChannel](https://servicechannel.zendesk.com/hc/en-us). Это позволит службе поддержки правильно настроить подключение единого входа SAML на обоих сторонах.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 

### <a name="creating-a-servicechannel-test-user"></a>Создание тестового пользователя ServiceChannel

Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. Для настройки полной подготовки пользователей обратитесь в [службу поддержки ServiceChannel](https://servicechannel.zendesk.com/hc/en-us).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к ServiceChannel.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ServiceChannel, сделайте следующее.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **ServiceChannel**.

    ![Настройка единого входа](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент ServiceChannel на панели доступа, вы автоматически войдете в приложение ServiceChannel.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_203.png
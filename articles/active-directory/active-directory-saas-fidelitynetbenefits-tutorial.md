---
title: Руководство по интеграции Azure Active Directory с Fidelity NetBenefits | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Fidelity NetBenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 19d18dd0d88fa3e66c60191314d0d1f80aa415c1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342269"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Руководство по интеграции Azure Active Directory с Fidelity NetBenefits

В этом руководстве описано, как интегрировать Fidelity NetBenefits с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Fidelity NetBenefits обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Fidelity NetBenefits.
- Вы можете включить автоматический вход пользователей в Fidelity NetBenefits (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Fidelity NetBenefits, вам потребуется:

- подписка Azure AD;
- подписка Fidelity NetBenefits с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Fidelity NetBenefits из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Добавление Fidelity NetBenefits из коллекции
Чтобы настроить интеграцию Fidelity NetBenefits с Azure AD, необходимо добавить Fidelity NetBenefits из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Fidelity NetBenefits из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Fidelity NetBenefits**, выберите **Fidelity NetBenefits** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Fidelity NetBenefits в списке результатов](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Fidelity NetBenefits с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Fidelity NetBenefits соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Fidelity NetBenefits.

Для этого в Fidelity NetBenefits нужно сопоставить **пользователя** с **пользователем Azure AD**.

Чтобы настроить и проверить единый вход Azure AD в Fidelity NetBenefits, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Fidelity NetBenefits](#create-a-fidelity-netbenefits-test-user)** нужно для того, чтобы в Fidelity NetBenefits также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Fidelity NetBenefits.

**Чтобы настроить единый вход Azure AD в Fidelity NetBenefits, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Fidelity NetBenefits** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Fidelity NetBenefits** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес:

    для тестовой среды: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    для рабочей среды: `urn:sp:fidelity:geninbndnbparts20`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес:

    для тестовой среды: `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    для рабочей среды: `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. Приложение Fidelity NetBenefits ожидает утверждения SAML в определенном формате. Мы сопоставили **идентификатор пользователя** с **user.userprincipalname**. Его можно сопоставить с **employeeid** или любым другим утверждением, применимым к организации в качестве **идентификатора пользователя**. На следующем снимке экрана приведен пример.

    ![Атрибут Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Fidelity NetBenefits поддерживает статическую и динамическую федерацию. Статическая федерация означает, что не будет использоваться JIT-подготовка пользователей на основе SAML, а динамическая федерация означает, что такая JIT-подготовка пользователей поддерживается. Для использования JIT-подготовки клиентам нужно добавить несколько дополнительных утверждений в Azure AD, таких как дата рождения пользователя и т. д. Эти сведения предоставляются [группой поддержки Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com), которая должна включить динамическую федерацию для вашего экземпляра.
    
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Fidelity NetBenefits** щелкните **Настроить Fidelity NetBenefits**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Чтобы настроить единый вход на стороне **Fidelity NetBenefits**, нужно отправить скачанный **XML-файл метаданных**, **URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** [группе поддержки Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Создание тестового пользователя Fidelity NetBenefits

В этом разделе описано, как создать пользователя Britta Simon в Fidelity NetBenefits. В случае, если вы создаете статическую федерацию, обратитесь к [группе поддержки Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com), чтобы создать пользователей на платформе Fidelity NetBenefits. Перед использованием единого входа необходимо создать и активировать этих пользователей. 

Для динамической федерации пользователи создаются с помощью JIT-подготовки. Для использования JIT-подготовки клиентам нужно добавить несколько дополнительных утверждений в Azure AD, таких как дата рождения пользователя и т. д. Эти сведения предоставляются [группой поддержки Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com), которая должна включить динамическую федерацию для вашего экземпляра.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Fidelity NetBenefits.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Fidelity NetBenefits, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Fidelity NetBenefits**.

    ![Ссылка на Fidelity NetBenefits в списке приложений](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Fidelity NetBenefits" на панели доступа, вы автоматически войдете в приложение Fidelity NetBenefits.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_203.png


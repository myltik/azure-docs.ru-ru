---
title: Руководство по интеграции Azure Active Directory с Compliance ELF | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Compliance ELF.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 32cb676393558079baad9bb0fc26d2c4daaa3519
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Руководство по интеграции Azure Active Directory с Compliance ELF

Из этого руководства вы узнаете, как интегрировать Compliance ELF с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Compliance ELF обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Compliance ELF.
- Вы можете включить автоматический вход пользователей в Compliance ELF (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Compliance ELF, вам потребуется:

- подписка Azure AD;
- подписка Compliance ELF с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Compliance ELF из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-compliance-elf-from-the-gallery"></a>Добавление Compliance ELF из коллекции
Чтобы настроить интеграцию Compliance ELF с Azure AD, необходимо добавить Compliance ELF из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Compliance ELF из коллекции, выполните инструкции ниже.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Compliance ELF**, выберите **Compliance ELF** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Compliance ELF в списке результатов](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Compliance ELF при помощи тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в Compliance ELF соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Compliance ELF.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Compliance ELF.

Чтобы настроить и проверить единый вход Azure AD в Compliance ELF, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создав тестового пользователя Compliance ELF](#create-a-compliance-elf-test-user)** Britta Simon, мы сможем связать его с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Compliance ELF.

**Чтобы настроить единый вход Azure AD в Compliance ELF, выполните инструкции ниже.**

1. На портале Azure на странице интеграции с приложением **Compliance ELF** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. Чтобы настроить приложение в режиме, инициированном **IDP**, в разделе **Compliance ELF Domain and URLs** (Домен и URL-адреса приложения Compliance ELF) сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Compliance ELF](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес в формате `https://sso.cordium.com`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Домен и URL-адреса единого входа для приложения Compliance ELF](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.complianceelf.com`
    
    > [!NOTE] 
    > Это значение приведено для справки. Замените эти значения фактическим URL-адресом для входа. Для получения этого значения обратитесь в [службу поддержки Compliance ELF](mailto:support@complianceelf.com).

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложения**, и вставьте его в Блокнот.
    
    ![Настройка единого входа](./media/active-directory-saas-complianceelf-tutorial/tutorial_metadataurl.png)
     
6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-complianceelf-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **Compliance ELF**, отправьте [группе поддержки Compliance ELF](mailto:support@complianceelf.com) **URL-адрес метаданных федерации приложения**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-compliance-elf-test-user"></a>Создание тестового пользователя Compliance ELF

Из этого раздела вы узнаете, как создать пользователя Britta Simon в Compliance ELF. Для добавления пользователей на платформе Compliance ELF свяжитесь с [группой поддержки Compliance ELF](mailto:support@complianceelf.com). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе пользователю Britta Simon предоставляется разрешение на использование единого входа Azure для доступа к Compliance ELF.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Compliance ELF, выполните инструкции ниже.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Compliance ELF**.

    ![Ссылка на Compliance ELF в списке "Приложения"](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Compliance ELF на панели доступа, вы автоматически войдете в приложение Compliance ELF.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_203.png

